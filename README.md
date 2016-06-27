PayPal Library
==============

This is a PHP library for building applications that use PayPal. It contains classes to help deal with IPN requests, PDT requests and generating PayPal buttons.

IPN Requests
------------

[PayPal IPN and PDT Variables Reference](https://developer.paypal.com/webapps/developer/docs/classic/ipn/integration-guide/IPNandPDTVariables/).

```php
<?php
$ipn = new PayPal\IpnRequest;
$ipn->set_timeout(5);
$ipn->allow_test_ipns(true);

$ipn->process(function($post_data) {
  // Save it to the database or something
});
```

PDT Requests
------------

[PayPal IPN and PDT Variables Reference](https://developer.paypal.com/webapps/developer/docs/classic/ipn/integration-guide/IPNandPDTVariables/).

```php
<?php
//$paypal_pdt_token - From PayPal when enabling PDT
// $tx - from PayPal when returning from a sale.  Usually $_GET['tx']
$pdt = new PayPal\PdtRequest($paypal_pdt_token,$tx);
$pdt->set_timeout(5);
$pdt->enable_sandbox(true);

$pdt->process(function($transaction_data) {
  // Show the user a receipt
  // $transaction_data - holds all of the values back from PayPal about the transaction.
}, function() {
  // Validation failed, show the user an error message or pull their receipt
  // from your database. Validation will fail after 3-5 successful verification
  // attempts.
})
```

PayPal Buttons
--------------

```php
<?php
// $your_public_cert, $your_private_key - Contents of your public cert that you upload to PayPal, 
// and your private key that you retain.
// $paypal_cert_id - The ID assigned to your cert after you upload it.
// $paypal_public_cert - Download from the PayPal site when you upload your cert.
// Useful links about this:
// Can be generated via instructions here: https://www.stellarwebsolutions.com/en/articles/paypal_button_encryption_php.php
// Also Useful: https://developer.paypal.com/docs/classic/paypal-payments-standard/integration-guide/encryptedwebpayments/
$button = new PayPal\EncryptedButton;
$button->set_certificate($your_public_cert, $your_private_key);
$button->set_certificate_id($paypal_cert_id);
$button->set_paypal_cert($paypal_public_cert);
$encrypted_text = $button->encrypt([
  'cmd'           => '_xclick',
  'business'      => '{your business ID or Email Address}',
  'lc'            => 'CA',
  'currency_code' => 'CAD',
  'no_shipping'   => '1',
  'no_note'       => '1',
  'custom'        => 'some_custom_data',
  'item_name'     => 'An Awesome Item',
  'amount'        => '123.45',
  'quantity'      => '1',
  'item_number'   => 'AWESOME-ITM-01',
  'tax'           => '0.00',
  'cancel_return' => 'URL to cancel'
]);
?>
<form method="post" action="https://www.paypal.com/cgi-bin/webscr">
  <input type="hidden" name="cmd" value="<?= $button->get_cmd() ?>">
  <input type="hidden" name="encrypted" value="<?= $encrypted_text ?>">
  <input type="submit" value="Checkout">
</form>

- OR - 

<form method="post" action="https://www.sandbox.paypal.com/cgi-bin/webscr">
  <input type="hidden" name="cmd" value="<?= $button->get_cmd() ?>">
  <input type="hidden" name="encrypted" value="<?= $encrypted_text ?>">
  <input type="submit" value="Checkout">
</form>
```

License
-------

This code is licensed under the MIT license.
