# How to Broadcast with XMTP

This tutorial will guide you on how to create a simple `Broadcast` that enables the user to broadcast messages to one or many specified Ethereum addresses.

![](video.gif)

#### Import XMTP Client and Ethers Library

The code starts by importing the required XMTP and Ethereum packages. This enables you to create an XMTP client and interact with the Ethereum blockchain.

```jsx
import { Client } from "@xmtp/xmtp-js";
import { ethers } from "ethers";
```

#### Set up the `walletAddresses` Array

The Broadcast function accepts a walletAddresses array. This array holds the Ethereum addresses that you intend to broadcast messages to.

```jsx
walletAddresses = [
  "0x93E2fc3e99dFb1238eB9e0eF2580EFC5809C7204",
  "0xa64af7F78DE39A238Ecd4ffF7D6D410DBACe2dF0",
];
```

#### Rate Limiting

Keep in mind that the XMTP client limits up to 1,000 publish requests per 5 minutes. It's important to be aware of this when broadcasting messages, especially when dealing with a large array of wallet addresses.

_For more information check the [FAQs](https://xmtp.org/docs/faq#rate-limiting)_

#### Check which addresses can receive messages

In the `handleBroadcastClick` function, the canMessage method checks which of the wallet addresses from the array can receive messages.

```jsx
// Create a new XMTP client with the signer and environment
const xmtp = await Client.create(signer, { env: env });
// Check if the client can message the provided wallet addresses
const broadcasts_canMessage = await xmtp.canMessage(walletAddresses);
```

_This will return an array of booleans that correspond to the wallet addresses. If the boolean is true, the address can receive messages. If the boolean is false, the address cannot receive messages._

#### Loop through wallet address array to broadcast

Here, you loop through the `walletAddresses` array. For each address that can receive messages, a new conversation is started and the message is sent.

```jsx
// Loop through the wallet addresses
for (let i = 0; i < walletAddresses.length; i++) {
  const wallet = walletAddresses[i];
  const canMessage = broadcasts_canMessage[i];
  // If the client can message the wallet address
  if (canMessage) {
    // Create a new conversation with the wallet address
    const conversation = await xmtp.conversations.newConversation(wallet);
    // Send the broadcast message
    const sent = await conversation.send(broadcastMessage);
    console.log("Sent", sent);
    // If a success callback was provided, call it with the sent message
    if (onMessageSuccess) {
      onMessageSuccess(sent);
    }
  }
}
```

#### Modal Interface

A modal pops up when the user opts to send a message. This modal contains a text area for the user to input their message.

```jsx
{
  showPopup && (
    <div style={styles.ubContainer}>
      <textarea
        style={styles.textArea}
        placeholder={placeholderMessage}
        value={broadcastMessage}
        onChange={(e) => setBroadcastMessage(e.target.value)}
        disabled={loading}
      />
    </div>
  );
}
```

#### Conclusion

By following these steps, you should be able to utilize XMTP for broadcasting messages to multiple Ethereum wallet addresses. Remember to be mindful of the rate limit of 1,000 publish requests per 5 minutes imposed by XMTP.
