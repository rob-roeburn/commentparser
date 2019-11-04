# Comment Parser

A parsing tool to process JSON data and produce visualisation.

![alt text](https://github.com/rob-roeburn/commentparser/blob/master/grab.png "Visualisation")

The app is built using the [Svelte](https://svelte.dev/) compiler.  There are two components and a store definition:

### Stores.js

This simply defines a writable store to pass subscribable JSON data between the components.

### FusionChart.svelte

This extends the FusionCharts functionality to provide visualisation, and also contains the main logic in parseJSON.  

A loading bool and embedded if statement keep the chart from being displayed until the data is parsed.  A basic loading overlay is displayed to the user.  A switch statement parses the retrieved JSON differently based on the config parameters in the main app.  The nodes are iterated through once for each comment.  For comment frequency, postId is extracted.  For email suffixes, a regular expression is applied to the email field to extract the TLD.  For the body keywords, the body is tokenised on both space and newline, then each keyword is iterated as for the other fields.

Once the desired fields are extracted from each comment, a ternary statement checks if the data already exists in the results array and inserts a new counter if the field doesn't exist, or increments the existing count if the field does exist.

Once the comments have been iterated, the data is formatted into a FusionCharts-compatible datasource and the loading bool is set to false so that the chart can be displayed.

### App.svelte

This defines the default endpoint and provides an input field so the target can be changed, if required.  The fetchEndpoint function retrieves the JSON asynchronously, and updates the store with the response data.  The rest of the file contains config for each FusionChart component, and invokes them beneath the input field.

## Built With

* [Svelte](https://svelte.dev/) - The compiler used
* [NPM](https://www.npmjs.com/) - Package management
* [FusionCharts](https://www.fusioncharts.com/) - Visualisation plugin
* [AWS Amplify](https://aws.amazon.com/amplify/) - Deployment

## Authors

* **Rob Young**

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details
