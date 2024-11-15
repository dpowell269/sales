<template>
  <div id="app">
    <!-- Editor Section -->
    <section class="editor">
      <h2>Flash Sale Staggered Banner Editor</h2>
      <form @submit.prevent="generateHtml">
        <!-- Background Image -->
        <div>
          <label for="background-image">Background Image URL:</label>
          <input
            v-model="bannerConfig.backgroundImage"
            id="background-image"
            type="text"
            placeholder="Enter image URL"
          />
        </div>

        <!-- Date Controls -->
        <div>
          <label for="start-date">Start Date:</label>
          <input v-model="bannerConfig.startDate" id="start-date" type="date" />
          <input v-model="bannerConfig.startTime" id="start-time" type="time" />
        </div>
        <div>
          <label for="end-date">End Date:</label>
          <input v-model="bannerConfig.endDate" id="end-date" type="date" />
          <input v-model="bannerConfig.endTime" id="end-time" type="time" />
        </div>

        <!-- Text Controls -->
        <div v-for="(threshold, index) in bannerConfig.thresholds" :key="index">
          <label>Threshold {{ index + 1 }}</label>
          <input v-model="threshold.title" placeholder="Title (e.g., 20% Off)" />
          <input v-model="threshold.subtitle" placeholder="Subtitle (e.g., Spend £60)" />
        </div>

        <!-- Links & Misc -->
        <div>
          <label for="background-link">Background Link:</label>
          <input v-model="bannerConfig.backgroundLink" id="background-link" type="text" />
        </div>
        <div>
          <label for="shop-link">Shop Button Link:</label>
          <input v-model="bannerConfig.shopLink" id="shop-link" type="text" />
        </div>

        <button type="button" @click="preview = true">Preview</button>
        <button type="submit">Export HTML</button>
      </form>
    </section>

    <!-- Preview Section -->
    <section v-if="preview" class="preview">
      <h2>Banner Preview</h2>
      <section
        class="flash-sale"
        :class="{ 'hidden-component': !isWithinDateRange() }"
        id="timed-flash-sale"
      >
        <div
          class="flash-sale-container"
          :style="{ backgroundImage: `url('${bannerConfig.backgroundImage}')` }"
        >
          <a :href="bannerConfig.backgroundLink" class="background-link"></a>
          <div class="thresholds">
            <div class="threshold" v-for="(threshold, index) in bannerConfig.thresholds" :key="index">
              <h2>{{ threshold.title }}</h2>
              <h3 :style="{ color: '#c5a842' }">{{ threshold.subtitle }}</h3>
            </div>
          </div>
          <p class="end-date">{{ bannerConfig.footerText }}</p>
          <p class="end-date">Ends {{ formattedEndDate }}</p>
          <div class="shop-buttons">
            <a :href="bannerConfig.shopLink" class="button">Shop All</a>
          </div>
          <p class="tcs">*T&Cs apply</p>
        </div>
      </section>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      preview: false,
      bannerConfig: {
        startDate: "2024-10-20",
        startTime: "17:00:00",
        endDate: "2024-11-22",
        endTime: "09:00:00",
        backgroundImage:
          "https://media.theperfumeshop.com/pws/client/images/website/2024/black-friday/BF-BACKGROUND-DESKTOP.png",
        backgroundLink: "/offers/all-offers/special-offers/c/W30041",
        shopLink: "/offers/all-offers/special-offers/c/W30041",
        footerText: "ON SELECTED BRANDS",
        thresholds: [
          { title: "20% Off", subtitle: "When you spend £60" },
          { title: "25% Off", subtitle: "When you spend £100" },
          { title: "30% Off", subtitle: "When you spend £150" },
        ],
      },
    };
  },
  computed: {
    formattedEndDate() {
      if (!this.bannerConfig.endDate || !this.bannerConfig.endTime) return "";
      return new Date(
        `${this.bannerConfig.endDate}T${this.bannerConfig.endTime}`
      ).toLocaleString();
    },
  },
  methods: {
    isWithinDateRange() {
      const now = new Date();
      const start = new Date(
        `${this.bannerConfig.startDate}T${this.bannerConfig.startTime}`
      );
      const end = new Date(
        `${this.bannerConfig.endDate}T${this.bannerConfig.endTime}`
      );
      return now >= start && now <= end;
    },
    generateHtml() {
      const html = `
        <section class="flash-sale hidden-component" id="timed-flash-sale">
          <div class="flash-sale-container" style="background-image: url('${this.bannerConfig.backgroundImage}')">
            <a href="${this.bannerConfig.backgroundLink}" class="background-link"></a>
            <div class="thresholds">
              ${this.bannerConfig.thresholds
                .map(
                  (threshold) => `
                <div class="threshold">
                  <h2>${threshold.title}</h2>
                  <h3 style="color: #c5a842;">${threshold.subtitle}</h3>
                </div>
              `
                )
                .join("")}
            </div>
            <p class="end-date">${this.bannerConfig.footerText}</p>
            <p class="end-date">Ends ${this.formattedEndDate}</p>
            <div class="shop-buttons">
              <a href="${this.bannerConfig.shopLink}" class="button">Shop All</a>
            </div>
            <p class="tcs">*T&Cs apply</p>
          </div>
        </section>
      `;
      const blob = new Blob([html], { type: "text/html" });
      const link = document.createElement("a");
      link.href = URL.createObjectURL(blob);
      link.download = "flash-sale-banner.html";
      link.click();
    },
  },
};
</script>


<style>
	.full-width-force {
		width: 100% !important;
		max-width: 1200px !important;
		margin: auto !important;
	}

	.hidden-component {
		display: none;
	}

	section.flash-sale {
		background: #ffffff;
		padding: 0 10px;
		border-radius: 4px;
		width: 100%;
		max-width: 1200px;
		margin: auto;
	}

	section.flash-sale .flash-sale-container {
		background: #ebe4dc;
		border-radius: 4px;
		width: 100%;
		max-width: 1200px;
		margin: 20px auto;
		padding: 20px;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		background-repeat: no-repeat;
		background-image: url('https://media.theperfumeshop.com/pws/client/images/website/2024/black-friday/BF-BACKGROUND-DESKTOP.png');
		background-size: cover;
		background-position-y: 0;
		position: relative;
		text-decoration: none;
	}

	@media only screen and (min-width: 768px) {
		section.flash-sale .flash-sale-container {
			background-size: cover;
			background-position-y: 0px;
		}
	}

	section.flash-sale .flash-sale-container h2 {
		margin: 0 auto;
		padding: 0;
		color: #fff;
		width: fit-content;
		text-align: center;
		box-sizing: border-box;
		font-family: DIN Condensed, DIN Condensed Bold, system-ui;
		font-size: 48px;
		text-transform: uppercase;
	}

	section.flash-sale .flash-sale-container h3 {
		margin: 0 auto;
		padding: 0;
		color: #fff;
		width: fit-content;
		text-align: center;
		box-sizing: border-box;
		font-family: Mark My Words Clean, system-ui;
		font-size: 21px;
		font-weight: 400;
        /* transform: rotate(-5deg); */
	}

	section.flash-sale .flash-sale-container p.end-date {
		margin: 20px auto 0;
		padding: 0;
		color: #fff;
		text-align: center;
		font-family: Muli, arial, helvetica, sans-serif;
		font-size: 14px;
		font-weight: 400;
	}

	section.flash-sale .flash-sale-container .shop-buttons {
		display: grid;
		grid-template-columns: 1fr;
		gap: 10px;
	}

	section.flash-sale .flash-sale-container a.button {
		display: block;
		background: #000000;
		width: 100%;
		padding: 10px 25px;
		box-sizing: border-box;
		text-align: center;
		margin: 20px auto 0 auto;
		border-radius: 4px;
		text-decoration: none;
		color: #ffffff;
		font-family: Muli, arial, helvetica, sans-serif;
		font-size: 14px;
		text-transform: uppercase;
		cursor: pointer;
		border: solid 1px  #c5a842;
		transition-property: color, background-color, border-color, text-decoration-color, fill, stroke, opacity, box-shadow, transform, filter, backdrop-filter, -webkit-backdrop-filter;
		transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
		transition-duration: 0.15s;
		position: relative;
		overflow: hidden;
        z-index: 2;
	}

	section.flash-sale .flash-sale-container a.button:before {
		content: '';
		position: absolute;
		width: 100px;
		height: 100%;
		background-image: linear-gradient(120deg, rgba(255, 255, 255, 0) 30%, rgba(255, 255, 255, 0.8), rgba(255, 255, 255, 0) 70%);
		top: 0;
		left: -100px;
		animation-name: shine;
		animation-duration: 4s;
		animation-delay: 1.5s;
		animation-iteration-count: infinite;
	}

	section.flash-sale .flash-sale-container a.button:hover {
		background: #ffffff;
		color: #000000;
		cursor: pointer;
	}

	section.flash-sale .flash-sale-container p.tcs {
		margin: 20px auto 0;
		padding: 0;
		color: #fff;
		text-align: center;
		font-family: Muli, arial, helvetica, sans-serif;
		font-size: 10px;
		font-weight: 400;
		position: absolute;
		bottom: 5px;
		right: 5px;
	}

	section.flash-sale .flash-sale-container .thresholds {
		display: grid;
		grid-template-columns: 1fr;
		gap: 20px;
		width: 100%;
		max-width: 650px;
	}

	section.flash-sale .flash-sale-container .thresholds .threshold {
		animation-name: text-jump;
		animation-duration: 4s;
		animation-iteration-count: infinite;
	}

	section.flash-sale .flash-sale-container .thresholds .threshold:nth-of-type(2) {
		animation-delay: 0.5s;
	}

	section.flash-sale .flash-sale-container .thresholds .threshold:nth-of-type(3) {
		animation-delay: 1s;
	}

	@media only screen and (min-width: 768px) {
		section.flash-sale .flash-sale-container .thresholds {
			grid-template-columns: 1fr 1fr 1fr;
		}
	}

	@keyframes text-jump {
		0% {
			transform: translateY(0px);
		}
		10% {
			transform: translateY(-10px);
		}
		20% {
			transform: translateY(0px);
		}
		100% {
			transform: translateY(0px);
		}
	}

	@keyframes shine {
		0% {
			left: -100px;
		}
		20% {
			left: 100%;
		}
		100% {
			left: 100%;
		}
	}

    a.background-link {
  position: absolute;
  display: block;
  width: 100%;
  height: 100%;
  z-index: 1;
 }
</style>