# xmlseclibs 

xmlseclibs is a library written in PHP for working with XML Encryption and Signatures.

This project is a fork of [robrichards/xmlseclibs](https://github.com/robrichards/xmlseclibs) and adds support for RSASSA-PSS based XML signature algorithms via usage of the RSA implementation of [phpseclib](http://phpseclib.sourceforge.net/). We're using this in our fork of [lightSAML](https://github.com/brain-SCC/lightsaml).

## Requirements

This xmlseclibs fork requires PHP version 7.0 or greater.


## How to Install

Install with composer.

```json
{
    "repositories": [
        {
            "url": "https://github.com/brain-SCC/xmlseclibs.git",
            "type": "git"
        }
    ],
    "require": {
        "brainscc/xmlseclibs": "~3.0"
    }
}
```

## Basic usage

The example below shows basic usage of xmlseclibs, with a SHA-256 signature.

```php
use RobRichards\XMLSecLibs\XMLSecurityDSig;
use RobRichards\XMLSecLibs\XMLSecurityKey;

// Load the XML to be signed
$doc = new DOMDocument();
$doc->load('./path/to/file/tobesigned.xml');

// Create a new Security object 
$objDSig = new XMLSecurityDSig();

// Use the c14n exclusive canonicalization
$objDSig->setCanonicalMethod(XMLSecurityDSig::EXC_C14N);

// Sign using SHA-256
$objDSig->addReference(
    $doc, 
    XMLSecurityDSig::SHA256, 
    ['http://www.w3.org/2000/09/xmldsig#enveloped-signature']
);

// Create a new (private) Security key
$objKey = new XMLSecurityKey(XMLSecurityKey::RSA_SHA256, ['type'=>'private']);

// If key has a passphrase, set it using
// $objKey->passphrase = '<passphrase>';

// Load the private key
$objKey->loadKey('./path/to/privatekey.pem', TRUE);

// Sign the XML file
$objDSig->sign($objKey);

// Add the associated public key to the signature
$objDSig->add509Cert(file_get_contents('./path/to/file/mycert.pem'));

// Append the signature to the XML
$objDSig->appendSignature($doc->documentElement);

// Save the signed XML
$doc->save('./path/to/signed.xml');
```