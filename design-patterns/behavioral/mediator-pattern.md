# Mediator design pattern

Mediator design pattern is one of the important and widely used behavioral design pattern. Mediator enables decoupling of objects by introducing a layer in between so that the interaction between objects happen via the layer. If the objects interact with each other directly, the system components are tightly-coupled with each other that makes higher maintainability cost and not hard to extend. Mediator pattern focuses on providing a mediator between objects for communication and help in implementing lose-coupling between objects.

Air traffic controller is a great example of mediator pattern where the airport control room works as a mediator for communication between different flights. Mediator works as a router between objects and it can have it’s own logic to provide way of communication.

## Example

```java
class ATCMediator implements IATCMediator 
{ 
	private Flight flight; 
	private Runway runway; 
	public boolean land; 

	public void registerRunway(Runway runway) 
	{ 
		this.runway = runway; 
	} 

	public void registerFlight(Flight flight) 
	{ 
		this.flight = flight; 
	} 

	public boolean isLandingOk() 
	{ 
		return land; 
	} 

	@Override
	public void setLandingStatus(boolean status) 
	{ 
		land = status; 
	} 
} 

interface Command 
{ 
	void land(); 
} 

interface IATCMediator 
{ 

	public void registerRunway(Runway runway); 

	public void registerFlight(Flight flight); 

	public boolean isLandingOk(); 

	public void setLandingStatus(boolean status); 
} 

class Flight implements Command 
{ 
	private IATCMediator atcMediator; 

	public Flight(IATCMediator atcMediator) 
	{ 
		this.atcMediator = atcMediator; 
	} 

	public void land() 
	{ 
		if (atcMediator.isLandingOk()) 
		{ 
			System.out.println("Successfully Landed."); 
			atcMediator.setLandingStatus(true); 
		} 
		else
			System.out.println("Waiting for landing."); 
	} 

	public void getReady() 
	{ 
		System.out.println("Ready for landing."); 
	} 

} 

class Runway implements Command 
{ 
	private IATCMediator atcMediator; 

	public Runway(IATCMediator atcMediator) 
	{ 
		this.atcMediator = atcMediator; 
		atcMediator.setLandingStatus(true); 
	} 

	@Override
	public void land() 
	{ 
		System.out.println("Landing permission granted."); 
		atcMediator.setLandingStatus(true); 
	} 

} 

class MediatorDesignPattern 
{ 
	public static void main(String args[]) 
	{ 

		IATCMediator atcMediator = new ATCMediator(); 
		Flight sparrow101 = new Flight(atcMediator); 
		Runway mainRunway = new Runway(atcMediator); 
		atcMediator.registerFlight(sparrow101); 
		atcMediator.registerRunway(mainRunway); 
		sparrow101.getReady(); 
		mainRunway.land(); 
		sparrow101.land(); 
		
	} 
} 

```