<template>
<div class="flex-container">
<system-card v-for="cards  in cards_data" :key="cards.id" :data="cards"></system-card>

</div>
</template>

<script>
import systemCard from './components/systemCard.vue'
export default {
  components: { systemCard },

  data () {
    return {
      cpuUsage: 0,
      system: {},
      network: {},
      loading: true,
      uptime: 0,
      lastCPUTime: null,
      ramUsage: 0,
      flashUsage: 0,
      data: [],
      cards_data: {
        system: [{
          title: 'SYSTEM',
          data:
          [{ title: 'Router uptime', value: '1:31:20' },
            { title: 'Local device time', value: '2022-10-21 10:47:00' },
            { title: 'Memory Usage', value: 'zdczczd' },
            { title: 'Firmware version', value: 'RUTX_R_00.02.06.1' }]

        }],
        wan: [{ title: 'WAN', data: [{ title: 'Invalid', value: 'Wired (eth1)' }, { title: 'IP address', value: '192.168.10.1' }] }],
        lan: [
          {
            title: 'LAN',
            data: [{
              title: 'Type',
              value: 'Wired (br-la)'
            },
            {
              title: 'IP address',
              value: '192.168.1.1'
            }]
          }
        ],
        rne: [{
          title: 'RECENT NETWORK EVENTS',
          data:
          [{ title: 'Router uptime', value: '1:31:20' },
            { title: 'Local device time', value: '2022-10-21 10:47:00' },
            { title: 'Firmware version', value: 'RUTX_R_00.02.06.1' },
            { title: 'Firmware version', value: 'RUTX_R_00.02.06.1' }]

        }],
        rse: [{
          title: 'RECENT SYSTEM EVENTS',
          data:
          [{ title: 'Router uptime', value: '1:31:20' },
            { title: 'Local device time', value: '2022-10-21 10:47:00' },
            { title: 'Firmware version', value: 'RUTX_R_00.02.06.1' },
            { title: 'Firmware version', value: 'RUTX_R_00.02.06.1' }]

        }]
      }
    }
  },
  timers: {
    update: { time: 1000, autostart: true, repeat: true, immediate: true },
    updateCpuUsage: {
      time: 1000, autostart: true, immediate: true, repeat: true
    }
  },

  methods: {
    async update () {
      this.system = await this.$system.getInfo()
      const systemObj =
  [{ title: 'Router uptime', value: '%t'.format(this.system.uptime) },
    { title: 'Local device time', value: this.localTime },
    { title: 'Memory Usage', value: 'zdczczd' },
    { title: 'Firmware version', value: this.system.release && this.system.release.revision }]
      const lanObj = [{
        title: 'Type',
        value: 'Wired ' + (this.data[0].device)
      },
      {
        title: 'IP Address ',
        value: this.data[0].target
      }]
      this.cards_data.system[0].data = systemObj
      this.cards_data.lan[0].data = lanObj
    },

    async getSystemInfo () {
      this.system = await this.$system.getInfo()
      this.loading = false
    },
    updateCpuUsage () {
      this.$rpc.call('system', 'cpu_time').then((times) => {
        if (!this.lastCPUTime) {
          this.cpuUsage = 0
          this.lastCPUTime = times
          return
        }

        const idle1 = this.lastCPUTime[3]
        const idle2 = times[3]

        let total1 = 0
        let total2 = 0

        this.lastCPUTime.forEach((t) => {
          total1 += t
        })

        times.forEach((t) => {
          total2 += t
        })

        this.cpuUsage = Math.floor(((total2 - total1 - (idle2 - idle1)) / (total2 - total1)) * 100)
        this.lastCPUTime = times
      })
    }
  },
  created () {
    this.getSystemInfo()
    this.$rpc.call('network', 'routes').then(({ routes }) => {
      this.data = routes.map((v, i) => {
        v.key = i
        return v
      })
    })
  },
  computed: {

    localTime () {
      const d = new Date(this.system.localtime * 1000)
      const year = d.getFullYear()
      const month = (d.getMonth() + 1)
      const date = d.getDate()
      const hour = d.getHours()
      const min = d.getMinutes()
      const sec = d.getSeconds()
      return `${year}-${month}-${date} %02d:%02d:%02d`.format(hour, min, sec)
    }
  }

}
</script>
<style scoped>
.flex-container {
  display: flex;
  flex-wrap: wrap;
}
.flex-container > div {
  width: 380px;
  margin: 10px;

  }
</style>
