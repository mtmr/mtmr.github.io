---
layout: post
title: "RMI - Quick Writeup"
date: 2015-02-16 22:42:55 +0530
comments: true
categories: ["programming"] 
---

RMI execution at a glance..
--------------------------

This is intended to be a quick guide to execute RMI as means for understanding the concept. This is for lab only and is not meant to be taken as a real life guide for RMI exection. For that refer Core JAVA - Volume 2 by Cay Horstmann.

<!--more-->

Client side
-------------

* Extend and implement the Remote interface in java.rmi.*.
* Make sure your methods throw RemoteException and use either `Naming.lookup(rmi://localhost/you_class)` or `namingcontext.lookup(rmi://localhost/your_class)`. Here namingcontext is an object constructed using `Context namingcontext = new InitialContext();`.
* After looking up, use casting to get the object of the type you need.
* See the code below.

```java Warehouse.java

import java.rmi.*;
public interface Warehouse extends Remote
{
	double getPrice(String description) throws RemoteException;
}

```
This one demonstrates the second point.

```java WarehouseClient.java

import java.rmi.*;
import java.util.*;
import javax.naming.*;
public class WarehouseClient
{
	public static void main(String[] args) throws NamingException, RemoteException
	{
		Context namingcontext = new InitialContext();
		System.out.println("RMI registry bindings..");
		String url = "rmi://localhost/centralwarehouse";
		Warehouse centralwarehouse = (Warehouse) namingcontext.lookup(url);
		String descr = "Blackwell Toaster";
		double price = centralwarehouse.getPrice(descr);
		System.out.println(descr + " : " + price);

	}
}

```

Server Side...
-------------

* Implement the interface you defined in client side.
* Make sure you extend `UnicastRemoteObject` in `java.rmi.server.*`(Import it and extend it.)
* Bind the class to a rmi registry using similar methods you used for lookup. Replace lookup by bind. The main method show throw `NamingException` and `RemoteException`.
* Compile everything.

This demonstrates the first point.

```java WarehouseImpl.java

import java.rmi.*;
import java.rmi.server.*;
import java.util.*;
public class WarehouseImpl extends UnicastRemoteObject implements Warehouse
{
	private Map<String, Double> prices;
	public WarehouseImpl() throws RemoteException
	{
		prices = new HashMap<>();
		prices.put("Blackwell Toaster", 24.95);
		prices.put("ZapXpress Microwave Oven", 49.95);
	}
	public double getPrice(String description) throws RemoteException
	{
		Double price = prices.get(description);
		return price == null ? 0 : price;
	}
}

```


This demonstrates the Third point.

```java WarehouseServer.java

import java.rmi.*;
import javax.naming.*;

public class WarehouseServer
{
	public static void main(String[] args) throws RemoteException, NamingException
	{
		System.out.println("Constructing server Implementation...");
		WarehouseImpl centralwarehouse = new WarehouseImpl();
		System.out.println("Binding implementation to registry..");
		Context namingContext = new InitialContext();
		namingContext.bind("rmi:centralwarehouse", centralwarehouse);
		System.out.println("Waiting for Invocation from clients");
	}
}

```

After you compile everything, (Make sure they are in same folder). Do these in order.

* Open Three cmd windows or terminals.

* Start rmiregistry by executing `rmiregistry`

* Start the server program by `java WarehouseServer`

* Start the client program by `java WarehouseClient`

That's it. You *_DO NOT_* need `rmic`. It is deprecated since JAVA version 1.5. We have 1.6 in lab.
Pat yourselves on the back. You successfully executed RMI program. And also, realworld RMI isn't this simple. You have something called dynamic class loading that can really drive you up the wall if you aren't careful. 

The code examples are copied from Core Java - Volume 2 9th Edition by Cay Horstmann.


