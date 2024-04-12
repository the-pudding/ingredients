<script>
	import allRecipes from "$data/gpt_ingredients_cleaned.csv";
	import groupings from "$data/groupings_big.csv";
	import Ingredient from "$components/Ingredient.svelte";

	const ingredients = [
		"pasta",
		"potato",
		"gochujang",
		"harissa",
		"avocado",
		"kale",
		"dates"
	];
</script>

<div>
	{#each ingredients as ingredient}
		{@const variations = groupings
			.find((d) => d.canonical === ingredient)
			?.variations?.split("|")}
		{@const recipes = allRecipes.filter(
			(d) =>
				d.ingredients.split("|").includes(ingredient) ||
				variations.some((variation) => d.ingredients.includes(variation))
		)}
		<Ingredient name={ingredient} {variations} {recipes} />
	{/each}
</div>

<style>
	div {
		max-width: 600px;
		margin: auto;
	}
</style>
