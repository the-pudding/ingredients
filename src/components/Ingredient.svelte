<script>
	import { LayerCake, Svg } from "layercake";
	import Line from "$components/layercake/Line.svelte";
	import AxisX from "$components/layercake/AxisX.svg.svelte";
	import AxisY from "$components/layercake/AxisY.svg.svelte";
	import _ from "lodash";
	import * as d3 from "d3";
	import allRecipes from "$data/gpt_ingredients_cleaned.csv";

	export let name;
	export let variations;
	export let recipes;

	const getYear = (d) => {
		const parseDate = d3.timeParse("%m/%d/%Y");
		const dateObject = parseDate(d);
		return +dateObject.getFullYear();
	};

	let byYear = _.range(2010, 2024).map((year, i) => {
		const totalCount = allRecipes.filter(
			(d) => getYear(d.date) === year
		).length;
		const ingredientCount = recipes.filter(
			(d) => getYear(d.date) === year
		).length;
		return {
			year,
			totalCount,
			ingredientCount,
			percent: (ingredientCount / totalCount) * 100
		};
	});
	byYear = byYear.map((d, i) => {
		const previousYear = byYear[i - 1];
		const yoyChange =
			previousYear && previousYear.percent
				? ((d.percent - previousYear.percent) / previousYear.percent) * 100
				: 0;
		return {
			...d,
			yoyChange
		};
	});
</script>

<h3>{name}</h3>

<details>
	<summary>variations</summary>
	{#each variations as variation}
		<small>{variation}</small>
	{/each}
</details>

<details>
	<summary>recipes</summary>
	{#each _.orderBy(recipes, (d) => new Date(d.date)) as { id, date }}
		<small>{date} - {id.split("-").slice(1).join(" ")}</small>
	{/each}
</details>

{#if byYear}
	<div class="chart-container">
		<LayerCake
			padding={{ top: 8, right: 10, bottom: 20, left: 25 }}
			x={"year"}
			y={"yoyChange"}
			yDomain={[-300, 300]}
			data={byYear}
		>
			<Svg>
				<AxisX />
				<AxisY ticks={4} />
				<Line stroke="purple" curve={d3.curveCardinal} />
			</Svg>
		</LayerCake>
	</div>
{/if}

<style>
	.chart-container {
		height: 300px;
		width: 100%;
	}
	small {
		display: block;
	}
</style>
