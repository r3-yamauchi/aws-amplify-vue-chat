# AWS Amplify Vue Starter Chat sample with AWS AppSync

A VueJs starter app integrated with [aws-amplify](https://github.com/aws/aws-amplify) and AWS AppSync based on [aws-samples/aws-amplify-vue](https://github.com/aws-samples/aws-amplify-vue).

<img src="docs/assets/img/aws-amplify-vue-sample.png" width="880px" />

[![Deploy to Amplify Console](docs/assets/img/Deploy_to_Amplify_Console.svg)](https://console.aws.amazon.com/amplify/home#/deploy?repo=https://github.com/r3-yamauchi/aws-amplify-vue-chat)

* [Getting Started](#getting-started)
* [AWS Amplify Integration Explained](#aws-amplify-integration-explained)
  - [Setup AWS Amplify](#setup-aws-amplify)
  - [Auth Routing](#auth-routing)
  - [Storage Components](#storage-components)
* [License](#license)

## Getting Started

1. Clone project and install dependencies

```bash
$ git clone https://github.com/r3-yamauchi/aws-amplify-vue-chat.git
$ cd aws-amplify-vue-chat
$ npm install
```

2. Copy your `aws-exports` file into the src directory, or intialize a new [AWS Amplify CLI](https://github.com/aws-amplify/amplify-cli) project:

```bash
$ npm install -g aws-amplify/cli

$ amplify init

$ amplify add auth
$ > Yes, use the default configuration.

$ amplify add storage
$ > Content (Images, audio, video, etc.)
...
$ > Auth users only
$ > read/write

$ amplify add api
$ > GraphQL
...
$ > Amazon Cognito User Pool
$ Do you have an annotated GraphQL schema? N
$ Do you want a guided schema creation? Y
$ > Single object with fields (e.g. "Todo" with id, name description)
$ Do you want to edit the schema now? Y

  type Todo @model {
  id: ID!
  note: String!
  done: Boolean
}

$ amplify push
$ Do you want to generate code for your newly created GraphQL API N

```

3. Start the project

```bash
$ npm start
```

Check http://localhost:8080/


4. Edit your `aws-exports` file in the src directory. Change AWS AppSync API endpoint to another "Chat App" api created by "Start from a sample project" on AWS AppSync management console.:

```
    "aws_appsync_graphqlEndpoint": "https://1abcde2fghij3klmnopqrs4tuv.appsync-api.ap-northeast-1.amazonaws.com/graphql",
```


## License

This library is licensed under the Apache 2.0 License.
