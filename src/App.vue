<template>
  <Header :header="this.header" />
  <div class="content-container">
    <MissionView
      :missions="this.missions"
      :missionMarkdown="this.current_md"
      :selected="this.mission_slug"
      v-on:missionSelected="handleMissionSelected"
    />

    <EventsView :events="this.events" />
    <section class="section-container" id="main-tab">
      <div class="main-tab-header" style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img :src="mainTabIcon" />
          <h1>{{ mainTabTitle }}</h1>
        </div>
        <TabButton
          v-for="item in this.options.panelOptions"
          :name="item"
          :hidden="item === this.options.mainPanel"
          :key="item"
          @click="selectMainPanel(item)"
        />
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <transition name="fade" mode="out-in">
          <PilotsView v-if="this.options.mainPanel === 'pilot'" :pilots="this.pilots" />
          <NPCView v-else-if="this.options.mainPanel === 'npc'" :npcs="this.npcs" />
          <GlossaryView v-else-if="this.options.mainPanel === 'glossary'" />
          <ClocksView v-else-if="this.options.mainPanel === 'clock'" :clocks="this.clocks" />
        </transition>
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
  <Footer />
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import MissionView from './components/layout/MissionView.vue';
import EventsView from './components/layout/EventsView.vue';
import PilotsView from './components/layout/PilotsView.vue';
import NPCView from './components/layout/NPCView.vue';
import ClocksView from './components/layout/ClocksView.vue';
import GlossaryView from './components/layout/GlossaryView.vue';
import TabButton from './components/TabButton.vue'

export default {
  components: {
    Header,
    Footer,
    MissionView,
    EventsView,
    PilotsView,
    NPCView,
    GlossaryView,
    ClocksView,
    TabButton,
  },

  data() {
    return {
      "mission_slug": "002",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "001",
          "name": "Blinkgate Repair",
          "status": "success"
        },        
        {
          "slug": "002",
          "name": "All Work and No Play",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "Nano-Sun",
          "alias": "Senatorius Armstrong",
          "code": "b7cc4795-e301-41d2-a4a4-a6f8e998ca18//ecf5c7f3-a3dd-4e1a-9eba-2670401d0180",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Fist Of The Patriot"
        },
        {
          "callsign": "Knight",
          "alias": "Regulus",
          "code": "f5c7cf90-b94f-4869-98c6-035e9be3cfdc//a9a80631-7154-4207-abf3-498957b77a7b",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Squire"
        },
        {
          "callsign": "Depussy",
          "alias": "Mattias Dezl",
          "code": "f5c7cf90-b94f-4869-98c6-035e9be3cfdc//a9a80631-7154-4207-abf3-498957b77a7b",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Seaphor"
        },
        {
          "callsign": "Walker",
          "alias": "Roth Agnesi",
          "code": "c9b2c383-682d-4969-bc67-59174e8e465f//5d5696df-8f42-4eab-9eed-a0c3db8c1f56",
          "corpro": "GMS",
          "frame": "Sagarmatha",
          "mech": "SF71H"
        },
   {
          "callsign": "LOOKOUT",
          "alias": "Jean-Jacques",
          "code": "e03e82a5-0aa4-460a-9e0a-d9a1473d173a",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "O'Doyle"
        },
      ],
      "npcs": [
        {
          "name": "Orpheus",
          "affiliation": "Union Auxillary Corps",
          "pronouns": "They/Them",
          "notes": "The transport ships NHP and your Eye in the sky."
        },
        {
          "name": "Winter",
          "affiliation": "Union Auxillary Corps",
          "pronouns": "She/Her",
          "notes": "A Humonculous built to manage the cryogenics bay, something has been off about her."
        },
      ],
      "header": {
        "planet": "P3-Blinkgate",
        "year": "5016u",
        "system": "P3-412",
        "gate": "P3-Prime",
        "ring": "Atlas-Line",
        "headerTitle": "Union",
        "headerSubtitle": "Auxillary Corp",
        "subheaderTitle": "Blinkgate Repair",
        "subheaderSubtitle": "Delta-Echo-Echo-Zulu",
      },
      "clocks": [
        {
          "name": "Time",
          "description": "How much longer can I hold out?",
          "help": "When this clock hits zero the mission will result in failure",
          "color": "#FD7777",
          "value": 1,
          "max": 8,
        },
      ],
      "options": {
        "eventsMarkdownPerMission": true,
        "mainPanel": "pilot",
        "panelOptions": [
          "pilot",
          "npc",
          "glossary",
          "clock"
        ]
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {
    mainTabTitle() {
      if (this.options.mainPanel === "pilot") return "Pilot Roster"
      if (this.options.mainPanel === "npc") return "Persons Registry"
      if (this.options.mainPanel === "glossary") return "Lexicon"
      if (this.options.mainPanel === "clock") return "Clocks"
    },
    mainTabIcon() {
      return `/icons/${this.options.mainPanel}-icon.svg`
    }
  },

  methods: {
    handleMissionSelected(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if (this.options.eventsMarkdownPerMission) {
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

      if (self.options.eventsMarkdownPerMission) {
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
    },
    selectMainPanel(panel) {
      this.options.mainPanel = panel;
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
