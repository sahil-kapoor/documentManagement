# Digital Property Network

Defines a business network where house sellers can list their properties for sale.

Note that this network **references** the digital property model, via an npm dependency declared in package.json:

```
"dependencies": {
    "digitalproperty-model": "latest"
  }
```

This allows the model to be shared across different types of business networks, and allows the model to have a lifecycle that is independent of the business network.

## What should I do with this npm module?
It is expected that this npm module would be associated with a CI pipeline and tracked as source code in something like GitHub Enterpise. The CI pipeline this would be able to run functional validation on the whole definition, and also be able to published the module to an NPM repository. This allows sharing of the module etc. 

For a production or QA runtime there are administrative steps (deploy, update, remove etc.) that are performed using this Business Network Definition on a running Hyperledger Fabric. The lifecycle at it's simplest is *deploy a network definition*, *update a network definition* and (potentially) *remove the network definition*. These actions are performed using the Business Network Archive - which is a single file that encapsulates all aspects of the Business Network Definition. It is the 'deployable unit'.

## Creating the BusinessNetwork.
*Step1:* Create a npm module

```
npm init
```
The important aspects of this are the name, version and description. The only dependancy that will be required is the NPM module that contains the model - see step 2.

*Step2:* Create the transaction functions

We need to create a standard JavaScript file to contain the transaction functions

```bash
\git\DigitialProperty-Model > touch lib/DigitalLandTitle.js
```

In this example the following is the implementation of the `registeryPropertyForSale` transaction

```javascript
'use strict';

/**
 * Process a property that is held for sale
 * @param {net.biz.digitalPropertyNetwork.RegisterPropertyForSale} propertyForSale the property to be sold
 * @transaction
 */
function onRegisterPropertyForSale(propertyForSale) {
    console.log('### onRegisterPropertyForSale ' + propertyForSale.toString());
    propertyForSale.title.forSale = true;

    return getAssetRegistry('net.biz.digitalPropertyNetwork.LandTitle').then(function(result) {
            return result.update(propertyForSale.title);
        }
    );
}
```

_FUTURE_
Create a file to hold the permissions access control inforation - create a `permissions.acl` file



## Work with the network
Once we have the network complete we can create a business network definition archive. This is the unit that will actually be deloyable to the HyperLedger Fabric.

There is a `composer archive` command that can be used to create and inspect these archives. The `composer network` command is then used to administer the business network archive on the Hyperledger Fabric.

### Creating an archive

The `composer archive create` command is used to create the archive. The `--archiveFile` option is used to specify the name of the archive file to create. If this is not specified then a default name will be used that is based on the identifier of the business network (sanitized to be suitable as a filename). For example `@example_digitalPropertyNetwork-0.1.2.bna`.

Please refer to the docs for `composer archive create` for more options.


```bash
composer archive create --archiveFile digitialLandTitle.bna --inputDir . --sourceType dir --sourceName DigitalLandTitle
```

Once you have this archive it can then be deployed to the HLF (which will assuming is all running for the moment)

```bash
composer network deploy --archiveFile  DigitalLandTitle.zip  --enrollId WebAppAdmin --enrollSecret DJY27pEnl16d
```


