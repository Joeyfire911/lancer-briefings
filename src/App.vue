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
      "mission_slug": "001",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "001",
          "name": "Blinkgate Repair",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "Jinzo",
          "alias": "Shayan Arsenal",
          "code": "462370be-bd0f-41c2-b667-cc75f3a59a96///NDL-C-DEEP-STATION//377308ad-ba23-410b-ae37-68a1fb5f8db4",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Type - Worst Beat"
        },
        {
          "callsign": "Kal",
          "alias": "Vier Flau",
          "code": "7cd700cc-c990-48ed-892f-e5468de724c4///NDL-C-DEEP-STATION//a98c3e28-ad4a-4f89-bcd9-501464e960da",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Necrophile"
        },
        {
          "callsign": "Starboard",
          "alias": "Maria",
          "code": "4be26ce9-923b-4069-b6c9-76437d4be455///NDL-C-DEEP-STATION//056940c6-8d55-4190-8e85-57caa043cb1a",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Scarlet Judicator"
        },
        {
          "callsign": "Badlands",
          "alias": "Marlene Gatsby",
          "code": "98ca9616-044e-4f87-b89b-aae4eb3387ec///NDL-C-DEEP-STATION//6f572259-6946-41bf-931a-e0543709e892",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Mayfly"
        },
        {
          "callsign": "Compadre",
          "alias": 'Clint "CC" Cruz',
          "code": "d1fdf62e-d81e-4e10-97c8-df3bc4860117///NDL-C-DEEP-STATION//5a4254aa-9fa2-42ca-a077-8f5bfd1e1ad3",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Rio Bravo"
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
          "description": "How much longer can Orpheus hold out?",
          "help": "When this clock hits zero the mission will result in failure",
          "color": "#FD7777",
          "value": 6,
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
