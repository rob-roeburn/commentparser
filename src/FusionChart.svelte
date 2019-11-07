<script>

import {storedJSON} from './Stores.js';
import FusionCharts from 'fusioncharts';
import Charts from 'fusioncharts/fusioncharts.charts';
import FusionTheme from 'fusioncharts/themes/fusioncharts.theme.fusion';
import SvelteFC, {fcRoot} from 'svelte-fusioncharts';
fcRoot(FusionCharts, Charts, FusionTheme);

export let config = {};
let fusionData = {};
let loading = true;

/**
 * parseJSON - process JSON into result array and then invoke visualisations
 */
const parseJSON = (targetJSON) => {
	// Only proceed if JSON valid
	if (targetJSON !== 0) {
		// Reset loading boolean to true and initialise results array
		loading = true;
		let resultData = [];
		// Processing code - iterate over each comment once
		targetJSON.forEach(function(comment) {
			// switch control - each config type requires a different parsing approach
			switch (config.type) {
				case 'comments':
					// increment postId in result array if it exists, or create if not
					resultData[comment[config.element]] = typeof(resultData[comment[config.element]]) === 'undefined' ? resultData[comment[config.element]] = 1 : resultData[comment[config.element]] += 1;
					break;
				case 'suffixes':
					// Use regex to extract email suffix, then increment array if the suffix exists, or create if not
					let emailSuffix = comment[config.element].replace(/[A-z0-9._]+@[A-z0-9._]+\./, '');
					resultData[emailSuffix] = typeof(resultData[emailSuffix]) === 'undefined' ? resultData[emailSuffix] = 1 : resultData[emailSuffix] += 1;
					break;
				case 'keywords':
					// Tokenise body data on both space and newline, iterate over each body element
					for (let bodyElement of comment[config.element].split(/[\n]|[\ ]/)) {
						// increment tokenised body text element in array if it exists, or create if not
						resultData[bodyElement] = typeof(resultData[bodyElement]) === 'undefined' ? resultData[bodyElement] = 1 : resultData[bodyElement] += 1;
					}
					break;
				default:
					// Fallthrough with error message to console
					console.log("Unsupported type.");
			}
		})
		let chartData = [];
		for (let element in resultData) {
			chartData.push({
				'label': element,
				'value': resultData[element].toString()
			})
		}
		// Build chart config to load into SvelteFC Fusion component
		fusionData = {
			id: config.type + 'Chart',
			type: config.chartType,
			width: '100%',
			height: '30%',
			dataSource: {
				'chart': {
					'caption': config.caption,
					'showPercentInTooltip': '1',
					'xAxisName': config.xAxisName,
					'yAxisName': config.yAxisName,
					'theme': 'fusion'
				},
				'data': chartData // Apply reformatted data to fusionData element
			}
		};
		loading = false;
	}
};

// Subscribe stored JSON to update chart after parent async
storedJSON.subscribe(value => {
	parseJSON(value);
})

</script>

{#if loading}
	<p>Chart loading...</p>
{:else}
  <SvelteFC {...fusionData}/>
{/if}
