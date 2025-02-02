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

In this article we are going to create an economy simulation between cities. The first solution uses **Dictionaries**, the second solution uses **Classes** and the third solution uses **Dictionaries** and **Classes**.

## Solution 1: Dictionaries

```gdscript
extends Node

var City: Dictionary = {}
var cities: Array = ["Barcelona", "Marsella"]
var products: Array = ["Wheat", "Corn"]

# Main simulation loop
func _ready():
	initialize_data()  # Initialize cities and products


	# Print initalized data
	for city in cities:
		for product in products:
			print("%s:\t\t%d\t\t%d\t\t%0.2f\t\t%0.2f" % [
				product,
				City[city]["production"][product],
				City[city]["stock"][product],
				City[city]["demand"][product],
				City[city]["price"][product]
			])

	# Simulate 10 steps
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
			City[city]["demand"][product] = randf_range(0, 2)
			City[city]["price"][product] = randf_range(0, 2)


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
			# Calculate the trade amount
			var trade_amount = min(City[city1]["stock"][product], City[city2]["demand"][product])
			
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


## Solution 2: Classes

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


## Solution 3: Dictionaries and Classes

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

# Main simulation loop
func _ready():
	initialize_data()
	
	for city in cities:
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
	for city in cities:
		ciudades[city] = City.new(city, {}, {}, {}, {})
		for product in products:
			ciudades[city].production[product] = randi_range(0, 100)
			ciudades[city].demand[product] = randi_range(50, 500)
			ciudades[city].stock[product] = randi_range(10, 1000)
			ciudades[city].price[product] = randf_range(1.0, 10.0)


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
