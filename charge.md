# Charges

- [Creating charges](#creating_charges)
	- [Single charge](#single_charge)
	- [Charging a single item](#charging_single_item)
	- [Charging multiple items](#charging_multiple_item)
- [Shipping costs](#shipping_costs)
- [Metadata Attributes](#metadata_attributes)
    
<a name="creating_charges"></a>
## Creating charges

<a name="single_charge"></a>
#### Single charge

To create a single charge just pass the value **in cents** to the `charge` method:

	$user = new App\User;
	
	// Charging R$ 7,50 ...
	$user->charge( 750 ); 

> **Note:** Gerencianet only accepts charges **in cents**, and **500** as minimum value.

<a name="charging_single_item"></a>
#### Charging a single item

You can pass an array with the data's item to be charged:

    $product = [        
        'name' => 'Product X',
        'amount' => 1,
        'value' => 1000   // R$ 10,00
    ];
	
    $user->charge( $product ); 

<a name="charging_multiple_item"></a>
#### Charging multiple items

You can pass an array with a collection of items to be charged:

    $products = [
        [
            'name' => 'Product X',
            'amount' => 1,
            'value' => 1000		// R$ 10,00
        ],
        [
            'name' => 'Product Y',
            'amount' => 2,
            'value' => 2000		// R$ 20,00 x 2 = R$ 40,00
        ]
    ];
	
    $user->charge( $products ); // total = R$ 50,00

If all dones right, you will receive the response with code `200`:

	# response...
	Array
	(
	    [code] => 200
	    [data] => Array
	        (
	            [charge_id] => 1000
	            [total] => 5000
	            [status] => new
	            [custom_id] =>
	            [created_at] => 2016-07-04 19:33:44
	        )
	)

<a name="shipping_costs"></a>
## Shipping costs `Optional`

To add shipping costs to a charge just pass an array with those values.

If you want a shipping cost to be received by another person/account you must provide the `payee_code`. 

The Additional Shipping in the example below will be passed on to the referenced account after the payment. You can add as many as you want.

	$items = [
	    [
	        'name' => 'Product X',
	        'amount' => 1,
	        'value' => 1000
	    ],
	    [
	        'name' => 'Service Y',	        
	        'value' => 2000
	    ]
	];
	
	// Set the shipping costs...
	$options['shippings'] = [
	    [
	        'name' => 'My Shipping',
	        'value' => 2000
	    ],
	    [
	        'name' => 'Shipping to someone else',
	        'value' => 1000,
	        'payee_code' => 'GEZTAMJYHA3DAMBQGAYDAMRYGMZTGMBRGI'
	    ]
	];	
	
	// Then charge...
	$user->charge( $items, $options )


<a name="metadata_attributes"></a>
## Metadata attributes `Optional`

You can pass additional data for the charge:
		
	$item = [		    
		'name' => 'T-Shirt 001',        
		'value' => 2500
	];
	
	$options['metadata'] = [
	    'custom_id' => '001-RED',
	    'notification_url' => 'http://my_domain.com/notification'
	];
	
	$user->charge( $item, $options ); 

The `custom_id` property can be used to set your own reference to the charge.

The `notification_url` property will be used for sending notifications once things happen with charges statuses, as when it's payment was approved, for example.

