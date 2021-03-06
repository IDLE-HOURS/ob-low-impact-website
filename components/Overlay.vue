<template>
  <div :class="'bg--grey overlay overlay--' + open">
    <header class="overlay__header">
      <a :href="mainSiteLink" @click="trackGA()" class="overlay__header--switch">
        <span><img src="~/assets/svg/switch_icon.svg" alt="Switch to regular store"></span>Switch to regular store
      </a>
      <h5 @click="closeOverlay()">Close</h5>
    </header>

    <section class="overlay__body">
      <div class="overlay__body--main">
        <div class="">
          <ImpactMeter />
          <h5 class="overlay__carbon">Carbon intensity: {{carbonIntensity.forecast}}g CO2/kWh</h5>
        </div>
        <div class="overlay__text--container">
          <h6 class="overlay__carbon--desc">Our server’s carbon intensity is currently <span class="overlay__carbon-lvl">{{ carbonIndexName }}</span>. This site will continuously adapt to reflect the amount of renewable energy it’s running on.</h6>
          <ul class="overlay__carbon-indices">
            <li v-for="carbonIndex in carbonIndices" :class="(carbonIndex.key === carbonIntensity.index ? 'active' : '')">
              <h6>{{carbonIndex.name}}</h6>
              <h6>{{carbonIndex.description}}</h6>
            </li>
          </ul>
        </div>
      </div>
    </section>

    <div class="overlay__questions">
      <h5 class="text--green">What, why, how?</h5>
      <img class="overlay__arrow-down" src="~/assets/svg/arrow_down.svg" alt="Arrow pointing down">
    </div>

    <section class="bg--white overlay__internet">
      <h2 class="text--left text__internet">The internet uses electricity.</br>Quite a bit.</h2>
      <img class="image--data-center" src="~/assets/svg/data_center.svg" alt="Internet energy consumption illustration">

      <div class="overlay__questions--container">
        <div class="overlay__questions--description text--left" v-for="(item, index) in threeQuestions">
          <span>{{ item.question }}</span>
          <div class="">
            <h6>{{ item.description }}</h6>
            <h6 @click="closeOverlay()" class="overlay__questions--link"><nuxt-link v-if="index === 2"  :to="'/' + $store.state.activeCurrency + '#manifesto'">Read the manifesto</nuxt-link></h6>
          </div>

        </div>
      </div>
    </section>

    <Footer :currentBytes="footerData.currentBytes" :currentSavingsMultiplier="footerData.currentSavingsMultiplier" :currentPage="footerData.currentPage" :totalSavings="footerData.totalSavings"/>
  </div>
</template>

<script>
import Vue from 'vue'
import Footer from '~/components/Footer.vue'
import ImpactMeter from '~/components/ImpactMeter.vue'
import mainSiteMap from '~/assets/json/mainSiteMap.json'

export default Vue.extend({
  name: 'Overlay',
  props: {
    open: Boolean,
    carbonIntensity: Object,
    footerData: Object,
    mainSiteLink: String
  },
  components: {
    Footer,
    ImpactMeter
  },
  data() {
    return {
      carbonIndices: [
        {
          key: 'very-low',
          name: 'Lowest',
          description: 'Full resolution photos available'
        },
        {
          key: 'low',
          name: 'Low',
          description: 'No animations available'
        },
        {
          key: 'moderate',
          name: 'Medium',
          description: 'Low quality photos available'
        },
        {
          key: 'high',
          name: 'High',
          description: 'Only illustrations available'
        },
        {
          key: 'very-high',
          name: 'Highest',
          description: 'Our site closes down'
        }
      ],
      threeQuestions: [
        {
          question: 'What?',
          description: 'The Low Impact version of our website reduces the carbon emissions associated with online shopping by up to 70% in comparison to our regular site. This is done by getting rid of most of the data-heavy elements like images and videos.'
        },
        {
          question: 'Why?',
          description: 'The Internet is accountable for 3.7% of the world’s carbon emissions. This is because an enormous amount of electricity is required to run data transfer centers around the world.'
        },
        {
          question: 'How?',
          description: 'We have formulated a set of 10 guidelines to help radically limit the amount of data transfer required to run an online store or website. We call it “The Low Impact Manifesto”.'
        }
      ]
    }
  },
  methods: {
    closeOverlay: function () {
      this.$emit('closed', true)
    },
    trackGA() {
      ga('send', 'event', 'LIW: Switched to Regular store', 'Click', 'Clicked on overlay link')
    }
  },
  computed: {
    carbonIndexName () {
      let carbonName = this.carbonIndices.find((a) => {
        return a.key === this.$props.carbonIntensity.index
      })
      if(carbonName) return carbonName.name.toLowerCase()
      else return '...'
    }
  }
})
</script>

<style lang="scss">
@import "~assets/scss/variables.scss";
@import "~assets/scss/mixins.scss";

.overlay {
  background: map-get($colors, 'bgGrey');
  height: 100vh;
  overflow-y: scroll;
  position: fixed;
  transition: top 600ms ease-in-out;
  top: 110%;
  width: 100vw;

  &.overlay--true {
    top: 0px;
  }

  .manifesto__heading--dramatic {
    margin-bottom: 90px;
    margin-top: 90px;

    span {
      -webkit-text-fill-color: map-get($colors, 'green');;
    }

    @include screenSizes(phone) {
      margin-bottom: 50px;
      margin-top: 50px;
    }

    @include screenSizes(tabletPortrait) {
      flex-direction: column;
      display: flex;
      text-align: left;
      width: 75vw;
      margin: 30px auto 70px;
      font-size: 47px;

      span:nth-child(1) {
        text-align: center;
      }
      span:nth-child(2) {
        text-align: right;
      }
    }
  }
}

.overlay__header {
  align-items: center;
  display: flex;
  justify-content: space-between;
  margin: 20px;

  .overlay__header--switch {
    display: flex;
    align-items: center;
    flex-direction: row;

    img {
      margin-right: 7px;
    }

    @include screenSizesMin(tabletPortrait) {
      display: none;
    }
  }

  h5 {
    cursor: pointer;
  }
}

.overlay__body {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 70vh;

  @include screenSizes(phoneSmall) {
    margin-top: 40px;
  }

  .overlay__body--main {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: flex-end;

    @media screen and (max-width: 960px) {
      width: 100%;
      flex-direction: column;
      align-items: center;
    }
  }

  .overlay__text--container {
    position: absolute;
    display: flex;
    flex-direction: row;
    width: 95vw;
    padding: 30px 0;
    justify-content: space-between;
    margin: auto;
    align-items: center;
    max-width: 1400px;

    @media screen and (max-width: 960px) {
      padding: 0;
      width: 100%;
      position: unset;
      flex-direction: column;
    }

    .overlay__carbon--desc {
      max-width: 340px;
      text-align: left;
      padding-top: 20px;
      padding-bottom: 20px;
      font-size: 14px;

      @media screen and (max-width: 960px) {
        width: 100%;
        max-width: 400px;
        text-align: center;
      }
    }

    .overlay__carbon-indices {
      list-style: none;
      padding: 0;
      text-align: left;
      margin-top: 20px;
      width: 25vw;
      display: flex;
      flex-direction: column;
      min-width: 307px;

      @include screenSizes(desktopSmall) {
        width: auto;
        max-width: 400px;
      }

      @media screen and (max-width: 960px) {
        max-width: fit-content;
      }

      li:not(.active) {
        opacity: 0.25;
        font-weight: normal;

        h6:first-child, h6:nth-child(2) {
          font-weight: normal;
        }
      }

      li {
        display: flex;
        flex-direction: row;
        justify-content: space-between;
        padding: 5px 0;


        h6:first-child {
          margin-right: 25px;
          flex-basis: 25%;
          font-weight: bold;
          font-size: 14px;

          @include screenSizes(phoneSmall) {
            margin-right: 15px;
          }
        }

        h6:nth-child(2) {
          flex-basis: 75%;
          font-weight: bold;
          font-size: 14px;
        }
      }
    }
  }

  .spedometer__container {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: auto;
  }
}

.overlay__internet {
  padding-top: 100px;
}

.text__internet {
  max-width: 1300px;
  margin: auto;

  @include screenSizes(phone) {
    font-size: 21px;
  }
}

.overlay__carbon {
  .container-carbon--very-low & {
    color: map-get($colors, 'green');
  }
  .container-carbon--low & {
    color: map-get($colors, 'green');
  }
  .container-carbon--moderate & {
    color: map-get($colors, 'impactYellow');
  }
  .container-carbon--high & {
    color: map-get($colors, 'carbonHigh');
  }
}

$carbonLabels: 'very-low', 'low', 'moderate', 'high';

@each $label in $carbonLabels {
  .container-carbon--#{$label} .label--#{$label} {
    font-weight: bold;
    opacity: 1;
  }
}

.overlay__carbon-lvl {
  font-weight: bold;
}

.overlay__questions {
  margin: 40px auto;
  font-weight: bold;

  .overlay__arrow-down {
    width: 20px;
    margin-top: 10px;
  }
}

.image--data-center {
  margin: 40px auto;
  max-width: calc(100vw - 80px);
}

.overlay__questions--container {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  margin: 60px auto;
  max-width: 1300px;

  @include screenSizes(tabletPortrait) {
    flex-direction: column;
    align-items: center;
  }

  .overlay__questions--description {
    display: flex;
    flex-direction: row;
    align-items: flex-start;
    margin-bottom: 30px;
    max-width: 360px;

    span {
      margin-right: 30px;
      min-width: 45px;
      max-width: auto;
    }

    .overlay__questions--link {
      padding-top: 15px;
      color: map-get($colors, 'green');
      font-weight: bold;
    }
  }
}

</style>
