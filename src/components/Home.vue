<template>
  <div class="container shifted">
    <h1 class="h1">
      AWS Amplify Vue チャット・チーム7
    </h1>
    <h2 v-if="user">私は {{user.username}} さんです</h2>
    <div class="section">
      <input class="input" v-model="message" placeholder="メッセージ">
      <button type="button" @click="postMessage">送信</button>
      <li v-for="(value, key, index) in items" :key="index">
        {{ value.content }}
      </li>
    </div>
  </div>
</template>

<script>
import { API, Auth, graphqlOperation } from "aws-amplify";
import { v4 as uuid } from "uuid";

const addMessage = async message => {
  const createMessage = `
  mutation createMessage($id: ID!, $content: String, $conversationId: ID!, $createdAt: String!) {
    createMessage(id: $id, content: $content, conversationId: $conversationId, createdAt: $createdAt){
      __typename
      conversationId
      createdAt
      id
      sender
      content
      isSent
    }
  }`;
  return await API.graphql(graphqlOperation(createMessage, message));
};

const listMessages = async param => {
  const query = `
query getConversationMessages($conversationId: ID!, $after: String, $first: Int) {
  allMessageConnection(conversationId: $conversationId, after: $after, first: $first) {
    __typename
    nextToken,
    messages {
      __typename
      id
      conversationId
      content
      createdAt
      sender
      isSent
    }
  }
}`;
  return await API.graphql(graphqlOperation(query, param));
};

const listMessagesAsync = async that => {
  const param = {
    conversationId: "hello",
    after: null,
    first: 20
  };
  return listMessages(param).then(result => {
    // console.dir(result);
    if (
      result &&
      result.data &&
      result.data.allMessageConnection &&
      result.data.allMessageConnection.messages
    ) {
      console.dir(result.data.allMessageConnection.messages);
      that.items = result.data.allMessageConnection.messages;
    }
  });
};

const SubscribeToNewMessage = `
  subscription SubscribeToNewMessage($conversationId: ID!) {
    subscribeToNewMessage(conversationId: $conversationId) {
      content
      conversationId
      createdAt
      id
      isSent
      sender
    }
  }
`;

export default {
  name: "Home",
  data() {
    return {
      message: "こんにちは",
      items: [],
      user: null
    };
  },
  created: async function() {
    const that = this;
    return Auth.currentAuthenticatedUser()
      .then(result => {
        if (result && result.username) {
          // console.dir(result.username);
          that.user = result;
        }
        return listMessagesAsync(that);
      })
      .then(() => {
        const realtimeResults = async result => {
          if (
            result &&
            result.value &&
            result.value.data &&
            result.value.data.subscribeToNewMessage
          ) {
            console.log("realtime data: ", result.value.data);
            return listMessagesAsync(that);
          }
        };

        const subscription = API.graphql(
          graphqlOperation(SubscribeToNewMessage, { conversationId: "hello" })
        ).subscribe({
          next: realtimeResults,
          error: console.dir
        });
      });
  },
  methods: {
    postMessage: async function() {
      const that = this;
      const now = `${new Date().toISOString()}`;
      const id = `${now}_${uuid()}`;
      const message = {
        conversationId: "hello",
        content: this.message,
        createdAt: now,
        sender: that.user.username,
        isSent: false,
        id: id
      };
      console.log("new message", message);

      return addMessage(message);
      // .then(() => {
      //   return listMessagesAsync(that);
      // });
    }
  }
};
</script>
