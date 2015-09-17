Installation
============
::

    pip install pyinapp

Usage
=====

Currently pyinapp supports Google Play and App Store receipts validation.

Google Play:
------------
::

    from pyinapp import GooglePlayValidator, InAppValidationError


    bundle_id = 'com.yourcompany.yourapp'
    api_key = 'API key from the developer console'
    validator = GooglePlayValidator(bundle_id, api_key)

    try:
        purchase = validator.validate('receipt', 'signature')
        process_purchase(purchase)
    except InAppValidationError:
        """ process validation error """

App Store:
----------
::

    from pyinapp import AppStoreValidator, InAppValidationError


    bundle_id = 'com.yourcompany.yourapp'
    validator = AppStoreValidator(bundle_id)

    try:
        purchase = validator.validate('receipt')
        process_purchase(purchase)
    except InAppValidationError:
        """ process validation error """

Note that since iOS 7 Apple provides a new kind of receipt called Grand Unified Receipt which contains multiple receipts in itself. In this case you have to deal with a list of purchases. For the sake of convenience you can process purchases this way:

::

    def process_purchase(purchase):
        process(*purchase) if isinstance(purchase, list) else process(purchase)


    def process(*purchases):
        for p in purchases:
            print(p.transaction_id, p.product_id, p.quantity, p.purchased_at)
            """ for instance, save p to db and add a player some coins for it """


This approach allows to process both Google Play and App Store purchases the same way.

Purchase
========

Purchase is a universal wrapper for Google Play and App Store receipts. It contains the following fields:

- **transaction_id**: id of the purchase (**transaction_id** for App Store and **orderId** for Google Play)
- **product_id**: what product has been purchased (**product_id** for App Store and **productId** for Google Play);
- **quantity**: how many products have been purchased (**quantity** for App Store and always **1** for Google Play - there's no such field in Google Play receipt);
- **purchased_at**: when the product has been purchased, UNIX timestamp (**purchase_date** for App Store and **purchase_time** for Google Play).