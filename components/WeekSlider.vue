<template>
	<div id="week-slider">
		<!-- Slider -->
		<div class="slider">
			<p>
				<span class="month">{{ month }}</span>
				<span class="year">{{ year }}</span>
			</p>
			<ul class="day-names">
				<li>Po</li>
				<li>Ut</li>
				<li>St</li>
				<li>Št</li>
				<li>Pi</li>
				<li>So</li>
				<li>Ne</li>
			</ul>
			<transition-group
				class="slider-list"
				name="slider-list"
				tag="ul"
			>
				<li
					class="slider-list-item"
					:class="{highlight: day.fullDate == currentDate}"
					v-for="day in days"
					:key="day.fullDate"
				>
					{{ day.number }}
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
			month: "",
			year: ""
		};
	},
	computed: {
		currentDate() {
			return this._formatDate(new Date());
		}
	},

	watch: {
		offset() {
			this._setMonthAndYear();
		}
	},

	methods: {
		/**
		 * PRIVATE: SET MONTH & YEAR
		 */
		_setMonthAndYear() {
			const months = [
				"Jan",
				"Feb",
				"Mar",
				"Apr",
				"Máj",
				"Jún",
				"Júl",
				"Aug",
				"Sep",
				"Okt",
				"Nov",
				"Dec"
			];

			const monday = new Date(this.firstDay);
			monday.setDate(monday.getDate() + 7);

			const sunday = new Date(monday);
			sunday.setDate(sunday.getDate() + 7);

			// SET: this.month
			if (monday.getMonth() === sunday.getMonth()) {
				this.month = months[monday.getMonth()];
			} else {
				this.month = `
						${months[monday.getMonth()]}
						/
						${months[sunday.getMonth()]}
					`;
			}

			// SET: this.year
			if (monday.getFullYear() === sunday.getFullYear()) {
				this.year = monday.getFullYear();
			} else {
				this.year = `
                    ${monday.getFullYear()}
                    /
                    ${sunday.getFullYear()}
                `;
			}
		},
		/**
		 * PRIVATE: GET OBJECT THAT REPRESENTS DAY
		 */
		_getDay(date) {
			return {
				fullDate: this._formatDate(date),
				number: date.getDate() + "."
			};
		},
		/**
		 * PRIVATE: FORMAT DATE TO LOCALE DATE STRING
		 */
		_formatDate(date) {
			const dateFormatOptions = {
				day: "numeric",
				month: "numeric",
				year: "numeric"
			};

			return date.toLocaleDateString("sk-SK", dateFormatOptions);
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
				this.days.push(this._getDay(this.lastDay));
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
				this.days.unshift(this._getDay(this.firstDay));
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
				this.days.push(this._getDay(day));

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

		this._setMonthAndYear();
		this.generateAllDays();
	}
};
</script>


<style scoped>
#week-slider {
	width: 100%;
	max-width: 350px;
	color: #2d2d2d;
	text-align: center;
	padding: 1em 3em;
	margin: auto;
	border: 1px solid #e0e0e0;
	border-radius: 1em;
}

#week-slider p {
	margin-bottom: 0;
	display: flex;
	justify-content: space-between;
}

#week-slider p span {
	background-color: #ffc107;
	color: #2d2d2d;
	font-size: 10px;
	font-weight: bold;
	text-transform: uppercase;
	padding: 0.6em 2em;
	border-radius: 0.6em;
}

/**
*** DAY NAMES
*/
.day-names {
	list-style: none;
	display: flex;
	flex-wrap: wrap;
	padding-left: 0;
	margin-bottom: 0;
}
.day-names li {
	font-size: 12px;
	width: 50px;
}

/**
*** SLIDER
*/
.slider {
	width: 350px;
	overflow: hidden;
	margin: auto;
}

.slider-list {
	width: 1050px;
	position: relative;
	left: -350px;
	list-style: none;
	padding-left: 0;
	margin-top: 5px;
}

.slider-list-item {
	display: inline-block;
	width: 50px;

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
.slider-list-item:not(.highlight):hover {
	background-color: #e0e0e0;
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