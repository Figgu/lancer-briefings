<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission
            v-for="item in this.missions"
            :key="item.slug"
            :mission="item"
            :selected="this.mission_slug"
            @click="selectMission(item)"
          />
        </div>
      </div>
    </section>
    <section class="section-container" id="events" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute;"
    width="0"
    height="0"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix
          in="blur"
          mode="matrix"
          values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5"
          result="goo"
        />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup.ogg" type="audio/ogg" />
  </audio>
  <Footer/>
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "000",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "000",
          "name": "Calm before the storm",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "PilotOne",
          "alias": "PilotOneAlias",
          "code": "Union RM-4 Pilot Identification Record e361a934-4c86-40d1-8624-48089606b371 Tschenni:e361a934-4c86-40d1-8624-48089606b371//NDL-C-SORROW-ROYAL",
          "corpro": "Harrison Armory",
          "frame": "Tokugawa",
          "mech": "MechNamePilotOne"
        },
        {
          "callsign": "PilotTwo",
          "alias": "PilotTwoAlias",
          "code": "Union RM-4 Pilot Identification Record cf702f77-c719-43ee-9d9f-beaee7a0082b Howard.Julius:cf702f77-c719-43ee-9d9f-beaee7a0082b//NDL-C-THETA-ORBIT",
          "corpro": "Horus",
          "frame": "Pegasus",
          "mech": "MechNamePilotTwo"
        },
        {
          "callsign": "PilotThree",
          "alias": "PilotThreeAlias",
          "code": "Union RM-4 Pilot Identification Record b9d64bcf-23ac-44a8-8fdb-1e046cb22cdb Clowngenius:b9d64bcf-23ac-44a8-8fdb-1e046cb22cdb//NDL-C-THETA-LASH",
          "corpro": "IPS-Northstar",
          "frame": "Zheng",
          "mech": "MechNamePilotThree"
        },
        {
          "callsign": "PilotFour",
          "alias": "PilotFourAlias",
          "code": "Union RM-4 Pilot Identification Record c5f8f5bc-0723-4065-8dde-d18c6d2d26b5 Jiminy:c5f8f5bc-0723-4065-8dde-d18c6d2d26b5//NDL-C-STOLEN-CAIRN",
          "corpro": "IPS-Northstar",
          "frame": "Raleigh",
          "mech": "MechNamePilotFour"
        },
        {
          "callsign": "PilotFive",
          "alias": "PilotFiveAlias",
          "code": "d1fdf62e-d81e-4e10-97c8-df3bc4860117///NDL-C-DEEP-STATION//5a4254aa-9fa2-42ca-a077-8f5bfd1e1ad3",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "MechNamePilotFive"
        },
      ],
      "header": {
        "planet": "Taldris IV",
        "year": "5014u",
        "system": "Cascadia",
        "gate": "Cascade-Hood",
        "ring": "Cascade-Line",
        "headerTitle": "Sentinel Strike",
        "headerSubtitle": "Echo Team",
        "subheaderTitle": "Lancer Response",
        "subheaderSubtitle": "Fox-Indigo-Geronimo",
      },
      "options":{
        "eventsMarkdownPerMission": true
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if(this.options.eventsMarkdownPerMission){
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if(self.options.eventsMarkdownPerMission){
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 1902px;
  height: 910px;
  overflow: hidden;
}
</style>
