<template>
	<div id="week-slider">
		<!-- Slider -->
		<div class="slider">
			<transition-group
				class="slider-list"
				name="slider-list"
				tag="ul"
			>
				<li
					class="slider-list-item"
					:class="{highlight: day == currentDate}"
					v-for="day in days"
					:key="day"
				>
					{{ day }}
				</li>
			</transition-group>
		</div>

		<!-- Buttons-->
		<div class="buttons">
			<!-- BUTTON: Previous Week -->
			<button
				class="button"
				title="Týždeň dozadu"
				@click="prevWeek"
			>
				<i class="fas fa-chevron-left"></i>
			</button>
			<!-- BUTTON: Reset Slider -->
			<button
				class="button"
				title="Aktuálny týždeň"
				@click="reset"
			>
				<i class="fas fa-sync-alt"></i>
			</button>
			<!-- BUTTON: Next Week -->
			<button
				class="button"
				title="Týždeň dopredu"
				@click="nextWeek"
			>
				<i class="fas fa-chevron-right"></i>
			</button>
		</div>
	</div>
</template>


<script>
export default {
	data() {
		return {
			days: [],
			firstDay: null,
			lastDay: null,
			offset: 0,
			month: ""
		};
	},
	computed: {
		currentDate() {
			return this._formatDate(new Date());
		}
	},

	methods: {
		/**
		 * PRIVATE: FORMAT DATE TO LOCALE DATE STRING
		 */
		_formatDate(date) {
			const dateFormatOptions = {
				day: "numeric",
				month: "numeric",
				year: "numeric"
			};

			const stringDate = date
				.toLocaleDateString("sk-SK", dateFormatOptions)
				.split(". ");
			const [day, month, year] = stringDate;

			return `${day}.${month}.${year.slice(2)}`;
		},
		/**
		 * RESET SLIDER (SHOW ACTUAL WEEK)
		 */
		reset() {
			if (this.offset === 0) return;

			let index = 0;
			const count = Math.abs(this.offset);
			const action = this.offset < 0 ? this.nextWeek : this.prevWeek;

			while (index < count) {
				action();
				index++;
			}
		},
		/**
		 * SHOW NEXT WEEK
		 */
		nextWeek() {
			this.offset += 1;

			for (let index = 0; index < 7; index++) {
				this.firstDay.setDate(this.firstDay.getDate() + 1);
				this.lastDay.setDate(this.lastDay.getDate() + 1);

				this.days.shift();
				this.days.push(this._formatDate(this.lastDay));
			}
		},
		/**
		 * SHOW PREVIOUS WEEK
		 */
		prevWeek() {
			this.offset -= 1;

			for (let index = 0; index < 7; index++) {
				this.lastDay.setDate(this.lastDay.getDate() - 1);
				this.firstDay.setDate(this.firstDay.getDate() - 1);

				this.days.pop();
				this.days.unshift(this._formatDate(this.firstDay));
			}
		},
		/**
		 * GENERATE ALL DAYS
		 */
		generateAllDays() {
			const day = new Date(this.firstDay);
			let index = 0;

			day.setDate(day.getDate() - 1);

			while (index < 21) {
				day.setDate(day.getDate() + 1);
				this.days.push(this._formatDate(day));

				index++;
			}
		}
	},

	mounted() {
		// SET: First day of the actual week
		const monday = new Date();
		monday.setDate(monday.getDate() - monday.getDay() + 1);

		// INIT: this.firstDay
		this.firstDay = new Date(monday);
		this.firstDay.setDate(monday.getDate() - 7);

		// INIT: this.lastDay
		this.lastDay = new Date(monday);
		this.lastDay.setDate(monday.getDate() + 13);

		this.generateAllDays();
	}
};
</script>


<style scoped>
#week-slider {
	color: #2d2d2d;
	text-align: center;
}

/**
*** SLIDER
*/
.slider {
	width: 560px;
	overflow: hidden;
	margin: auto;
}

.slider-list {
	width: 1680px;
	position: relative;
	left: -560px;
	list-style: none;
	padding-left: 0;
}

.slider-list-item {
	display: inline-block;
	width: 80px;

	font-size: 12px;
	text-align: center;
	font-weight: 500;

	padding: 0.6em 0;
	border-style: solid;
	border-width: 1px;
	border-color: #e0e0e0;
	border-right: none;
	border-left: none;
	box-sizing: border-box;
}
.slider-list-item.highlight {
	background-color: #ffc107;
	border-color: #ffc107;
}

/**
*** BUTTONS
*/
.buttons {
	width: 100%;
}

.button {
	cursor: pointer;
	width: 30px;
	height: 30px;
	background-color: #e0e0e0;
	font-size: 10px;
	border: none;
	border-radius: 30px;
	transition: all 0.24s;
}
.button:hover {
	background-color: #ffc107;
}
.button:focus {
	outline: none;
}
.button:active {
	transform: scale(0.8);
}

/**
*** SLIDER TRANSITIONS
*/
.slider-list-item {
	display: inline-block;
	transition: all 0.24s;
}
.slider-list-enter,
.slider-list-leave-to {
	opacity: 0;
}
.slider-list-leave-active {
	position: absolute;
}
</style>