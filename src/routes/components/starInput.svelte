<script lang="ts">
	import { onMount } from 'svelte';

	export let rating = 0;
	export let isIndicatorActive = true;
	export let style = {
		styleStarWidth: 20,
		styleEmptyStarColor: '#737373',
		styleFullStarColor: '#ffd219'
	};
	$: rating;

	let emptyStar = 0;
	let fullStar = 1;
	let totalStars = 5;
	let stars: any = [];

	function getStarPoints() {
		let centerX = style.styleStarWidth / 2;
		let centerY = style.styleStarWidth / 2;
		let innerCircleArms = 5; // a 5 arms star
		let innerRadius = style.styleStarWidth / innerCircleArms;
		let innerOuterRadiusRatio = 2.5; // Unique value - determines fatness/sharpness of star
		let outerRadius = innerRadius * innerOuterRadiusRatio;
		return calcStarPoints(centerX, centerY, innerCircleArms, innerRadius, outerRadius);
	}

	function calcStarPoints(
		centerX: any,
		centerY: any,
		innerCircleArms: any,
		innerRadius: any,
		outerRadius: any
	) {
		let angle = Math.PI / innerCircleArms;
		let angleOffsetToCenterStar = 60;
		let totalArms = innerCircleArms * 2;
		let points = '';
		for (let i = 0; i < totalArms; i++) {
			let isEvenIndex = i % 2 == 0;
			let r = isEvenIndex ? outerRadius : innerRadius;
			let currX = centerX + Math.cos(i * angle + angleOffsetToCenterStar) * r;
			let currY = centerY + Math.sin(i * angle + angleOffsetToCenterStar) * r;
			points += currX + ',' + currY + ' ';
		}
		return points;
	}

	function initStars() {
		for (let i = 0; i < totalStars; i++) {
			stars.push({
				raw: emptyStar,
				percent: emptyStar + '%',
				value: i + 1
			});
		}
	}

	function calcStarFullness(starData: any) {
		let starFullnessPercent = starData.raw * 100 + '%';
		return starFullnessPercent;
	}

	function setStars() {
		let fullStarsCounter = Math.floor(rating);

		for (let i = 0; i < 5; i++) {
			if (fullStarsCounter !== 0) {
				stars[i].raw = fullStar;
				stars[i].percent = calcStarFullness(stars[i]);
				fullStarsCounter--;
			} else {
				let surplus = Math.round((rating % 1) * 10) / 10; // Support just one decimal
				let roundedOneDecimalPoint = Math.round(surplus * 10) / 10;
				stars[i].raw = roundedOneDecimalPoint;
				return (stars[i].percent = calcStarFullness(stars[i]));
			}
		}
	}

	function clearStars() {
		for (let i = 0; i < 5; i++) {
			stars[i].raw = emptyStar;
			stars[i].percent = calcStarFullness(stars[i]);
		}
	}

	function getFullFillColor(starData: any) {
		return starData.raw !== emptyStar ? style.styleFullStarColor : style.styleEmptyStarColor;
	}

	onMount(() => {
		initStars();
		setStars();
	});

	function onClickHandler(e: any) {
		rating = e.target.value;
		clearStars();
		setStars();
	}
</script>

<div class="star-container">
	<div class="star-rating">
		{#each stars as star}
			<label>
				<input
					type="radio"
					name="ratingStar"
					id={star.value}
					value={star.value}
					on:click={(e) => onClickHandler(e)}
				/>
				<svg
					class="star-svg"
					style="fill: url(#gradient{star.raw});height:{style.styleStarWidth};
          width:{style.styleStarWidth}"
				>
					<polygon points={getStarPoints()} style="fill-rule:nonzero;" />
					<defs>
						<linearGradient id="gradient{star.raw}">
							<stop
								id="stop1"
								offset={star.percent}
								stop-opacity="1"
								stop-color={getFullFillColor(star)}
							/>
							<stop
								id="stop2"
								offset={star.percent}
								stop-opacity="0"
								stop-color={getFullFillColor(star)}
							/>
							<stop
								id="stop3"
								offset={star.percent}
								stop-opacity="1"
								stop-color={style.styleEmptyStarColor}
							/>
							<stop
								id="stop4"
								offset="100%"
								stop-opacity="1"
								stop-color={style.styleEmptyStarColor}
							/>
						</linearGradient>
					</defs>
				</svg>
			</label>
		{/each}
		{#if isIndicatorActive}
			<div class="indicator">{rating.toFixed(1)}</div>
		{/if}
	</div>
</div>

<style>
	.star-rating {
		display: flex;
		align-items: center;
	}
	.star-rating {
		display: flex;
		align-items: center;
	}
	.star-container {
		display: flex;
		flex-direction: column;
		align-items: center;
	}
	.indicator {
		font-size: 1rem;
	}
	.star-container:not(:last-child) {
		margin-right: 5px;
	}
	label > input {
		/* Menyembunyikan radio button */
		visibility: hidden;
		position: absolute;
	}
	label > input + svg {
		/* style gambar */
		cursor: pointer;
		border: 2px solid transparent;
	}
</style>
