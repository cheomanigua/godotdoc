---
weight: 5350
title: "Economy"
description: "How to simulate economy between cities"
icon: "article"
date: "2025-02-02T13:01:07+02:00"
lastmod: "2025-02-02T13:01:07+02:00"
draft: false
toc: true
---

In this article we are going to create an economy simulation between cities. We present four solutions:

1. The first solution uses only **Dictionaries**.
2. The second solution uses a **Class** and a **Dictionary** to store the instances of the class.
3. The third solution is slighthy different implementation of the second solution.
4. The forth solution uses two **Classes** and an two **Arrays** to store the instances of the class.

## Solution 1. Dictionaries

```gdscript
extends Node

#var cities: Array = ["Barcelona", "Tarragona", "Valencia", "Perpiñan", "Marsella", "Livorno", "Napoles", "Venecia", "Cartagena", "Genova"]
#var products: Array = ["Wheat", "Corn", "Fruit", "Wine", "Oil", "Fish", "Wool", "Iron", "Gold", "Silver"]

var cities: Array = ["Barcelona", "Marsella"]
var products: Array = ["Wheat", "Corn"]
var products_base_price: Dictionary = {"Wheat": 1.0, "Corn": 2.0}
var City: Dictionary
# Main simulation loop
func _ready():
	initialize_data()  # Initialize cities and products
	for city in cities:
		print(city)
		for product in products:
			print("%s:\t\t%d\t\t%d\t\t%d\t\t%0.2f" % [
				product,
				City[city]["production"][product],
				City[city]["stock"][product],
				City[city]["demand"][product],
				City[city]["price"][product]
			])

	for step in range(10):  # Simulate for 10 steps (you can adjust this)
		print("Step %d" % step)
		for i in range(cities.size()):
			for j in range(i + 1, cities.size()):
				trade_between_cities(cities[i], cities[j])
		simulate_economy()  # Calculate and print the wealth of each city


# Initialize city data for products
func initialize_data():
	for city in cities:
		City[city] = {}
		City[city]["production"] = {}
		City[city]["stock"] = {}
		City[city]["demand"] = {}
		City[city]["price"] = {}
		for product in products:
			City[city]["production"][product] = randi_range(0, 100)
			City[city]["stock"][product] = randi_range(0, 1000)
			City[city]["demand"][product] = randf_range(0, 200)
			City[city]["price"][product] = randf_range(0.0, 2.0)


func adjust_price(city: String, product: String):
	var supply = City[city]["stock"][product]
	var demand = City[city]["demand"][product]

	# Simple price adjustment based on supply and demand
	if supply > demand:
		# If supply exceeds demand, price drops
		City[city]["price"][product] -= City[city]["price"][product] * 0.05  # Decrease by 5%
	elif demand > supply:
		# If demand exceeds supply, price rises
		City[city]["price"][product] += City[city]["price"][product] * 0.05  # Increase by 5%

	# Ensure price stays within reasonable bounds
	City[city]["price"][product] = clamp(City[city]["price"][product], 5, 200)

# Function to simulate trade between two cities
func trade_between_cities(city1: String, city2: String):
	for product in products:
		if City[city1]["stock"][product] > 0 and City[city2]["demand"][product] > 0:
			var trade_amount = min(City[city1]["stock"][product], City[city2]["demand"][product])
			# Calculate the trade amount
			
			# Transfer goods from city1 to city2
			City[city1]["stock"][product] -= trade_amount
			City[city2]["stock"][product] += trade_amount
			
			# Adjust prices after trade
			adjust_price(city1, product)
			adjust_price(city2, product)

# Function to calculate wealth of each city
func calculate_wealth(city: String) -> float:
	var wealth = 0.0
	for product in products:
		wealth += City[city]["stock"][product] * City[city]["price"][product]  # Wealth is stock * price of each product
	return wealth

# Simulate the economy after trade
func simulate_economy():
	for city in cities:
		var wealth = calculate_wealth(city)
		print("City: %s, Wealth: %.2f" % [city, wealth])
```


## Solution 2. Class to Dictionary (v1)

```gdscript
extends Node

class City:
	var name: String = ""
	var production: Dictionary = {}  # stores how much of each product a city produces
	var demand: Dictionary = {}  # stores demand for each product in the city
	var stock: Dictionary = {}  # stores stock of products in each city
	var price: Dictionary = {}  # stores prices for each product
	func _init(iname, iproduction, idemand, istock, iprice) -> void:
		self.name = iname
		self.production = iproduction
		self.demand = idemand
		self.stock = istock
		self.price = iprice

var ciudades: Dictionary = {}
var cities: Array = ["Barcelona", "Tarragona"]
var products: Array = ["Wheat", "Corn"]

var barcelona: City = City.new("Barcelona", {}, {}, {}, {})
var tarragona: City = City.new("Tarragona", {}, {}, {}, {})


# Main simulation loop
func _ready():
	initialize_data()
	
	for city in ciudades:
		print(city.name)
		for product in products:
			print("%s:\t\t%d\t\t%d\t\t%d\t\t%0.2f" % [
				product,
				city.production[product],
				city.demand[product],
				city.stock[product],
				city.price[product]
			])

	#for step in range(10):  # Simulate for 10 steps (you can adjust this)
		#print("Step %d" % step)
		#for i in range(cities.size()):
			#for j in range(i + 1, cities.size()):
				#trade_between_cities(cities[i], cities[j])
		#simulate_economy()  # Calculate and print the wealth of each city

# Initialize city data for products
func initialize_data():
	#for city in cities:
	ciudades[barcelona] = barcelona
	ciudades[tarragona] = tarragona
	
	for city in ciudades:
		for product in products:
			city.production[product] = randi_range(0, 100)
			city.demand[product] = randi_range(50, 500)
			city.stock[product] = randi_range(10, 1000)
			city.price[product] = randf_range(1.0, 10.0)

# Function to adjust price based on supply and demand
func adjust_price(city: City, product: String):
	var supply = city.stock[product]
	var demand = city.demand[product]

	# Simple price adjustment based on supply and demand
	if supply > demand:
		# If supply exceeds demand, price drops
		city.price[product] -= city.price[product] * 0.05  # Decrease by 5%
	elif demand > supply:
		# If demand exceeds supply, price rises
		city.price[product] += city.price[product] * 0.05  # Increase by 5%

	# Ensure price stays within reasonable bounds
	city.price[product] = clamp(city.price[product], 5, 200)

# Function to simulate trade between two cities
func trade_between_cities(city1: City, city2: City):
	for product in products:
		if city1.stock[product] > 0 and city2.demand[product] > 0:
			# Calculate the trade amount
			var trade_amount = min(city1.stock[product], city2.demand[product])
			
			# Transfer goods from city1 to city2
			city1.stock[product] -= trade_amount
			city2.stock[product] += trade_amount
			
			# Adjust prices after trade
			adjust_price(city1, product)
			adjust_price(city2, product)

# Function to calculate wealth of each city
func calculate_wealth(city: City) -> float:
	var wealth = 0.0
	for product in products:
		wealth += city.stock[product] * city.price[product]  # Wealth is stock * price of each product
	return wealth

# Simulate the economy after trade
func simulate_economy():
	for city in ciudades:
		var wealth = calculate_wealth(city)
		print("City: %s, Wealth: %.2f" % [city.name, wealth])
```


## Solution 3. Class to Dictionary (v2)

```gdscript
extends Node

class Ciudad:
	var name: String = ""
	var production: Dictionary = {}  # stores how much of each product a city produces
	var demand: Dictionary = {}  # stores demand for each product in the city
	var stock: Dictionary = {}  # stores stock of products in each city
	var price: Dictionary = {}  # stores prices for each product
	func _init(iname, iproduction, idemand, istock, iprice) -> void:
		self.name = iname
		self.production = iproduction
		self.demand = idemand
		self.stock = istock
		self.price = iprice

var ciudades: Dictionary = {}
var cities: Array = ["Barcelona", "Tarragona"]
var products: Array = ["Wheat", "Corn"]

# Main simulation loop
func _ready():
	initialize_data()
	
	for city in cities:
		print(city)
		for product in products:
			print("%s:\t\t%d\t\t%d\t\t%d\t\t%0.2f" % [
				product,
				ciudades[city].production[product],
				ciudades[city].demand[product],
				ciudades[city].stock[product],
				ciudades[city].price[product]
			])

	for step in range(10):  # Simulate for 10 steps (you can adjust this)
		print("Step %d" % step)
		for i in range(cities.size()):
			for j in range(i + 1, cities.size()):
				trade_between_cities(cities[i], cities[j])
		simulate_economy()  # Calculate and print the wealth of each city

# Initialize city data for products
func initialize_data():
	
	# METHOD 1 - Creates access to instance in dictionary via keys: ciudades[city]
	for city in cities:
		ciudades[city] = Ciudad.new(city, {}, {}, {}, {})
		for product in products:
			ciudades[city].production[product] = randi_range(0, 100)
			ciudades[city].demand[product] = randi_range(50, 500)
			ciudades[city].stock[product] = randi_range(10, 1000)
			ciudades[city].price[product] = randf_range(1.0, 10.0)
	# METHOD 2 - Creates direct access to instance in dictionary. Check Solution 2 above.
	#for city in cities:
		#var temp = Ciudad.new(city, {}, {}, {}, {})
		#ciudades[temp] = temp
		#for product in products:
			#city.production[product] = randi_range(0, 100)
			#city.demand[product] = randi_range(50, 500)
			#city.stock[product] = randi_range(10, 1000)
			#city.price[product] = randf_range(1.0, 10.0)


# Function to adjust price based on supply and demand
func adjust_price(city: String, product: String):
	var supply = ciudades[city].stock[product]
	var demand = ciudades[city].demand[product]

	# Simple price adjustment based on supply and demand
	if supply > demand:
		# If supply exceeds demand, price drops
		ciudades[city].price[product] -= ciudades[city].price[product] * 0.05  # Decrease by 5%
	elif demand > supply:
		# If demand exceeds supply, price rises
		ciudades[city].price[product] += ciudades[city].price[product] * 0.05  # Increase by 5%

	# Ensure price stays within reasonable bounds
	ciudades[city].price[product] = clamp(ciudades[city].price[product], 5, 200)

# Function to simulate trade between two cities
func trade_between_cities(city1: String, city2: String):
	for product in products:
		if ciudades[city1].stock[product] > 0 and ciudades[city2].demand[product] > 0:
			# Calculate the trade amount
			var trade_amount = min(ciudades[city1].stock[product], ciudades[city2].demand[product])
			
			# Transfer goods from city1 to city2
			ciudades[city1].stock[product] -= trade_amount
			ciudades[city2].stock[product] += trade_amount
			
			# Adjust prices after trade
			adjust_price(city1, product)
			adjust_price(city2, product)

# Function to calculate wealth of each city
func calculate_wealth(city: String) -> float:
	var wealth = 0.0
	for product in products:
		wealth += ciudades[city].stock[product] * ciudades[city].price[product]  # Wealth is stock * price of each product
	return wealth

# Simulate the economy after trade
func simulate_economy():
	for city in cities:
		var wealth = calculate_wealth(city)
		print("City: %s, Wealth: %.2f" % [city, wealth])
```


## Solution 4. Class to Array

`Product.gd`

```gdscript
class_name Product
extends Resource

var name: String
var base_price: float
var market_price: float
var stock: int
var demand: float

func _ready():
	# Default product setup
	name = "Wheat"
	base_price = 10.0
	market_price = base_price
	stock = 0
```

<br>

`economy.gd`

```gdscript
extends Node

class City:
	var cname: String
	var inventory : Dictionary = {} # Key: Product name, Value: Quantity
	var demand : Dictionary = {} # Key: Product name, Value: Demand factor
	var price_modifiers : Dictionary = {} # Key: Product name, Value: Price modifier
	var city_id: int
	
var cities : Array = []
var products : Array = []
var products_base_price = { "Wheat": 2.0, "Corn": 2.0, "Fruit": 3.0, "Wine": 6.0, "Gold": 20.0}
var cities_array: Array = ["Barcelona", "Tarragona", "Valencia", "Perpiñan"]


func _ready():
	# Create 10 cities and 10 products
	for ciudad in cities_array:
		var city = City.new()
		city.cname = ciudad
		cities.append(city)

	for i in products_base_price.size():
		var product = Product.new()
		product.name = products_base_price.keys()[i]
		product.base_price = products_base_price.values()[i]
		#product.base_price = randf_range(5.0, 20.0)
		products.append(product)
	
	for city in cities:
		print("\n", city.cname)
		print("Product\t\tStock\tDemand\tPrice")
		for product in products:
			# Update demand and inventory
			city.demand[product.name] = randf_range(0.5, 2.0)  # Random demand fluctuation
			city.inventory[product.name] = int(randf_range(0, 1000))  # Random stock
			city.price_modifiers[product.name] = 1.0
			product.stock = city.inventory[product.name]
			product.demand = city.demand[product.name]
			# Recalculate prices based on supply and demand
			product.market_price = calculate_price(city, product)
			print("%s\t\t%d\t\t%0.2f\t%0.2f" % [product.name, product.stock, product.demand, product.market_price])


func _process(_delta):
	#Simulate economy behavior over time
	for city in cities:
		for product in products:
			# Update demand and inventory
			city.demand[product.name] = randf_range(0.5, 2.0)  # Random demand fluctuation
			city.inventory[product.name] = int(randf_range(0, 1000))  # Random stock
			product.stock = city.inventory[product.name]
			
			# Recalculate prices based on supply and demand
			product.market_price = calculate_price(city, product)

	# Optionally, trigger trade events between cities every few seconds
	if randf_range(0, 1) < 0.1:
		var city1 = cities[int(randf_range(0, cities.size()))]
		var city2 = cities[int(randf_range(0, cities.size()))]
		var product_to_trade = products[int(randf_range(0, products.size()))]
		trade(city1, city2, product_to_trade.name, int(randf_range(10, 100)))


func calculate_price(city: City, product: Product) -> float:
	var base_price = product.base_price
	var supply = city.inventory.get(product.name, 0)
	var demand = city.demand.get(product.name, 1.0)

	# Modify the price based on supply and demand
	var price_factor = 1.0 + ((demand - 1.0) * 0.2)  # Increase price with demand
	price_factor -= (supply / 1000.0)  # Decrease price with increased supply

	return base_price * price_factor


func trade(from_city: City, to_city: City, product_name: String, quantity: int):
	var product_from = from_city.inventory.get(product_name, 0)
	#var product_to = to_city.inventory.get(product_name, 0)

	if product_from >= quantity:
		var price = calculate_price(from_city, Product.new()) # Calculate price for the product
		var total_cost = price * quantity
		
		##Update inventories after trade
		from_city.inventory[product_name] -= quantity
		to_city.inventory[product_name] += quantity
		#
		###Optionally adjust prices based on trade volume
		from_city.price_modifiers[product_name] += 0.05  # Adjust price modifiers with trade
		to_city.price_modifiers[product_name] -= 0.05
		print("%s: %s" % [from_city.cname, from_city.price_modifiers])
		
		return total_cost
	else:
		return -1  # Not enough stock
```
