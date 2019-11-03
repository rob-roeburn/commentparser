<script>

// Import fusioncharts plugin and initialise fcRoot
import FusionCharts from 'fusioncharts';
import Charts from 'fusioncharts/fusioncharts.charts';
import FusionTheme from 'fusioncharts/themes/fusioncharts.theme.fusion';
import SvelteFC, {fcRoot} from 'svelte-fusioncharts';
fcRoot(FusionCharts, Charts, FusionTheme);

// Placeholder charts config - initialization prior to JSON being parsed
let comments = {};
let commentConfig = {
	id: 'commentChart',
	type: 'column2d',
	width: '100%',
	height: '30%',
	dataSource: {},
	renderAt: 'comment-container',
};

let suffixes = {};
let suffixConfig = {
	id: 'suffixChart',
	type: 'pie2d',
	width: '100%',
	height: '30%',
	dataSource: {},
	renderAt: 'suffix-container'
};

let keywords = {};
let keywordConfig = {
	id: 'keywordChart',
	type: 'column2d',
	width: '100%',
 	height: '30%',
	dataSource: {},
	renderAt: 'keyword-container'
};

// Define default endpoint
let endpoint = 'https://jsonplaceholder.typicode.com/posts/1/comments';

/**
* parseJSON - async function to retrieve JSON object from endpoint, process into result array and then invoke visualisations
*/
const parseJSON = async () => {
	// Await fetch of  endpoint URL
	const response = await fetch( endpoint );
	// Await response endpoint URL
	const responsejson = await response.json();
	// Initialise resultData array and main elements as empty objs
	let resultData = [];
	resultData['comments'] = {};
	resultData['suffixes'] = {};
	resultData['keywords'] = {};
  // Processing code - iterate over each comment once
	for (let comment of responsejson) {
    // Get postId from comment and set new comments[postId] element to 1, or increment if the element exists already
		resultData['comments'][comment.postId] = typeof(resultData['comments'][comment.postId]) === 'undefined' ?
			resultData['comments'][comment.postId] = 1 :
			resultData['comments'][comment.postId] += 1;
		// Apply regex to set emailSuffix variable to domain suffix only
		let emailSuffix = comment.email.replace(/[A-z0-9._]+@[A-z0-9._]+\./, '');
		// Set new suffixes[emailSuffix] element to 1, or increment if the element exists already
		resultData['suffixes'][emailSuffix] = typeof(resultData['suffixes'][emailSuffix]) === 'undefined' ?
			resultData['suffixes'][emailSuffix] = 1 :
			resultData['suffixes'][emailSuffix] += 1;
		// Tokenise body data on both space and newline, iterate over each body element
		for (let bodyElement of comment.body.split(/[\n]|[\ ]/)) {
			// Set new keywords[bodyElement] element to 1, or increment if the element exists already
			resultData['keywords'][bodyElement] = typeof(resultData['keywords'][bodyElement]) === 'undefined' ?
				resultData['keywords'][bodyElement] = 1 :
				resultData['keywords'][bodyElement] += 1;
		}
	}
	/**
	* Each comment iterated once - resultData now contains:
	* [comments] - each index is the post ID and the value is the number of posts per comment
	* [suffixes] - each index is the suffix ID and the value is the number of occurrences
	* [keywords] - each index is the keyword from the body and the value is the number of occurrences
	*/

  // Convert resultData elemenst to label/value data format for FusionCharts
	comments = Object.entries(resultData['comments']);
	let commentData = [];
	for (let comment in comments) {
		commentData.push({
			'label': comments[comment][0],
			'value': comments[comment][1].toString()
		})
	};
	commentConfig = {
		id: 'commentChart',
		type: 'column2d',
		width: '100%',
		height: '30%',
		renderAt: 'comment-container',
		dataSource: {
			'chart': {
				'caption': 'Comments Per Post',
				'showPercentInTooltip': '1',
				'xAxisName':'Post ID',
				'yAxisName':'Comments',
				'theme': 'fusion'
			},
			'data': commentData
		}
	};

	suffixes = Object.entries(resultData['suffixes']);
	let suffixData = [];
	for (let suffix in suffixes) {
		suffixData.push({
			'label': suffixes[suffix][0],
			'value': suffixes[suffix][1].toString()
		})
	};
	suffixConfig = {
		id: 'suffixChart',
		type: 'pie2d',
		width: '100%',
		height: '30%',
		renderAt: 'suffix-container',
		dataSource : {
			'chart': {
				'caption': 'Email Suffix Distribution',
				'showPercentInTooltip': '1',
				'theme': 'fusion'
			},
			'data': suffixData
		}
	};

	keywords = Object.entries(resultData['keywords']);
	let keywordData = [];
	for (let keyword in keywords) {
		keywordData.push({
			'label': keywords[keyword][0],
			'value': keywords[keyword][1].toString()
		})
	};
	keywordConfig = {
		id: 'keywordChart',
		type: 'column2d',
		width: '100%',
		height: '30%',
		renderAt: 'keyword-container',
		dataSource : {
			'chart': {
				'caption': 'Post Keyword Frequency',
				'showPercentInTooltip': '1',
				'theme': 'fusion'
			},
			'data': keywordData
		}
	};

};
// Invocation on load
parseJSON();
</script>

<style>
#endpoint {
	width: 60%;
}
</style>

<div id='endpoint-title'>Endpoint definition - press enter to reload : <input id='endpoint' bind:value={endpoint} on:change={parseJSON}></div >
<div id='comment-container'><SvelteFC {...commentConfig}/></div ><hr />
<div id='suffix-container'><SvelteFC {...suffixConfig}/></div><hr />
<div id='keyword-container' ><SvelteFC {...keywordConfig}/></div ><hr />
