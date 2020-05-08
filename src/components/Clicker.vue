<template>
    <v-card class="container" style="margin-top: 50px; min-height:auto">
        <v-container>
            <v-row no-gutters class="justify-space-between">
                <v-col>
                    <h1>Kohi Click Test</h1>
                    <p class="lead">This is the click test that was featured on Kohi. Simply click the box below as fast
                        as you can for a given amount of time, and you'll be given your average CPS. You can also see
                        how your CPS changes with every secound</p>


                </v-col>
                <v-spacer></v-spacer>
                <v-col>
                    <img src="https://mcrpg.imgix.net/images/kohi-click-test.jpg?h=200" alt="Kohi Click Test"/>
                </v-col>
            </v-row>
        </v-container>

        <h1>Settings</h1>
        <v-row>
            <v-col>
                <v-text-field
                        v-model="time"
                        label="Total test time (in seconds)"
                        required
                ></v-text-field>
            </v-col>
            <v-col>
                <v-text-field
                        v-model="cpsSampleTime"
                        label="Time between CPS measures (in seconds)"
                        required
                ></v-text-field>
            </v-col>
        </v-row>


        <h3 v-if="testDone">Results: <span id="cps">{{cps.toFixed(1)}}</span> CPS</h3>
        <h3 class="align-center" v-else>Clicks: {{clickCount}}</h3>

        <v-btn type="button" v-if="testDone" class="btn btn-primary" @click="reset()">Restart Test</v-btn>
        <v-btn :ripple="false" type="button" v-else class="btn btn-primary" style="height: 200px; width: 100%"
               @click="buttonClick()">Click Here
        </v-btn>
        <h4 v-if="testRunning">{{timeElapsed.toFixed(2)}}</h4>
        <plotly :data="plot" :options="options" :layout="layout" v-if="testRunning || testDone" id="graph"
                :key="testRunning"/>
    </v-card>
</template>

<script>
    import {Plotly} from "vue-plotly"

    export default {
        name: "Clicker",
        components: {
            Plotly
        },
        data: () => ({
            plot: null,
            options: {
                responsive: true,
            },
            time: 10,
            layout: {
                title: "CPS graph",
                plot_bgcolor: "#202020",
                paper_bgcolor: "#202020",
                grid: {rows: 1, columns: 2, pattern: 'independent'},
                legend: {
                    x: 1,
                    xanchor: 'right',
                    y: 1
                }
            },
            clickCount: 0,
            testDone: false,
            testRunning: false,
            lastCpsClick: 0,
            cpsSampleTime: 1,
            timeElapsed: 0,
            isBlockedRegion: false,
            cps: 0,
            lastClick: 0
        }),
        methods: {
            buttonClick: function () {
                if (!this.testRunning) {
                    this.start()
                } else {
                    this.clickCount++
                    this.lastCpsClick++
                    var time = new Date().getTime()
                    var elapsed = time - this.lastClick
                    this.lastClick = time

                    this.plot[0].y.push(elapsed)
                    this.plot[0].x.push(this.clickCount)
                }
            },
            start: function () {
                var me = this
                me.started = new Date().getTime()
                me.testRunning = true
                me.lastClick = new Date().getTime()

                const sampleTime = me.cpsSampleTime * 1000

                me.cpsTimer = setInterval(function () {
                    me.plot[1].x.push(me.timeElapsed)
                    me.plot[1].y.push(me.lastCpsClick * (1000 / sampleTime))

                    me.lastCpsClick = 0
                }, sampleTime / 2)

                me.timerID = setInterval(function () {
                    me.timeElapsed = (new Date().getTime() - me.started) / 1000.0

                    if (me.timeElapsed >= me.time)
                        me.stop()
                }, me.time)

                me.plot = [{
                    x: [],
                    y: [],
                    name: "Time between clicks",
                    type: "lines"
                }, {
                    x: [],
                    y: [],
                    name: "CPS",
                    xaxis: 'x2',
                    yaxis: 'y2',
                    type: "lines"
                }]
            },

            stop: function () {
                this.testRunning = false
                this.testDone = true
                this.cps = this.clickCount / this.time
                clearInterval(this.timerID)
                clearInterval(this.cpsTimer)
            },
            reset: function () {
                this.testRunning = false
                this.testDone = false
                this.clickCount = 0
                this.timeElapsed = 0
            }

        }
    }
</script>

<style scoped>

</style>