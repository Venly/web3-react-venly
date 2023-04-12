# @venly/web3-react-venly

Venly connector for [web3-react](https://www.npmjs.com/package/web3-react)

### Example

```javascript
import { initializeConnector } from '@web3-react/core'
import { Venly } from '@venly/web3-react-venly';

export const [venly, hooks] = initializeConnector<Venly>(
  (actions) =>
    new Venly({
      actions,
      options: {
        clientId: 'YOUR_CLIENT_ID',
        environment: 'staging', //optional, defaults to production
        skipAuthentication: false
      },
    })
)
```

## Documentation
The full documentation of the Venly Web3 Provider can be found here: https://docs.venly.io/widget/web3-provider/getting-started

## What is Venly Wallet
Not sure yet what Venly is all about? Be sure to check out our website: https://www.venly.io/