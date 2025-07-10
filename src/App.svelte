<script>

import {onMount} from 'svelte';
import FusionChart from './FusionChart.svelte';
import {storedJSON} from './Stores.js';

let endpoint = 'https://jsonplaceholder.typicode.com/comments';

// fetchEndpoint function will retrieve JSON from the endpoint and store it in the storedJSON variable
const fetchEndpoint = async () => {
	const response = await fetch(endpoint);
	const responsejson = await response.json();
	storedJSON.set(responsejson);
}

// Initialise config objects for each FusionChart component call
let comments = {
	type: 'comments',
	element: 'postId',
	chartType: 'column2d',
	caption: 'Comments Per Post',
	xAxisName: 'Post ID',
	yAxisName: 'Comments'
};
let suffixes = {
	type: 'suffixes',
	element: 'email',
	chartType: 'pie2d',
	caption: 'Email Suffix Distribution',
	xAxisName: '',
	yAxisName: ''
};
let keywords = {
	type: 'keywords',
	element: 'body',
	chartType: 'column2d',
	caption: 'Post Keyword Frequency',
	xAxisName: '',
	yAxisName: 'Frequency'
};

// Fetch endpoint on initial mount
onMount(function () {
	fetchEndpoint();
});

</script>

<div>Endpoint definition - press enter to reload : <input style="width:50%" bind:value={endpoint} on:change={fetchEndpoint}></div>

<div><FusionChart config={comments} /></div>
<div><FusionChart config={suffixes} /></div>
<div><FusionChart config={keywords} /></div>
