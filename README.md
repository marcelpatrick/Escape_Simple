# Escape_Simple

Here is a more simplified version. It is a scape room with a trigger volume. If the actor overlaps the trigger volume it opens a door:

## 1- Create the Trigger Volume and place it in your level

![image](https://user-images.githubusercontent.com/12215115/152528485-634d15ff-9fc3-4201-a559-23aa1c590454.png)

## 2- Select the door and create a new C++ component for it

![image](https://user-images.githubusercontent.com/12215115/152528657-d03c408e-e872-411c-86ac-7aeb721eea6f.png)

## 3- Code
In the header file, instantiate the trigger volume variable, the variable for the actor that will open the door and the angle to which the door will open

```cpp
private:

UPROPERTY(EditAnywhere)
ATriggerVolume* PressurePlate = nullptr;

UPROPERTY(EditAnywhere)
AActor* ActorThatOpens; 

UPROPERTY(EditAnywhere)
float OpenAngle = 180.f;
```
On begin play assign the actor pawn to the actor that opens variable

```cpp
void UOpenDoor::BeginPlay()
{
	Super::BeginPlay();

	ActorThatOpens = GetWorld()->GetFirstPlayerController()->GetPawn();

	UE_LOG(LogTemp, Warning, TEXT("open angle is %f"), OpenAngle);
}
```

On Tick, craete an if statement for when the trigger volume is overlapping with the actor 
If true, Call OpenDoor Function

```cpp
void UOpenDoor::TickComponent(float DeltaTime, ELevelTick TickType, FActorComponentTickFunction* ThisTickFunction)
{
	Super::TickComponent(DeltaTime, TickType, ThisTickFunction);

	if (PressurePlate && PressurePlate->IsOverlappingActor(ActorThatOpens))
	{
		OpenDoor(DeltaTime);
	}
	
}
```

The OpenDoor Function does
- 1- gets the actor yaw and assigns to a float
- 2- Initializes a rotator variable 
- 3- uses and interpolator between the current yaw and the open angle and assigns this to the yaw parameter of the rotator
- 4- sets the actor rotation to the open door variable that we just updated

```cpp
void UOpenDoor::OpenDoor(float DeltaTime)
{
	UE_LOG(LogTemp, Warning, TEXT("vamos abrir a porta"));

	float CurrentYaw = GetOwner()->GetActorRotation().Yaw;
	FRotator OpenDoor(0.f, 0.f, 0.f);
	OpenDoor.Yaw = FMath::FInterpTo(CurrentYaw, OpenAngle, DeltaTime, 1);
	GetOwner()->SetActorRotation(OpenDoor);

	UE_LOG(LogTemp, Warning, TEXT("door rotation is %s"), *GetOwner()->GetActorRotation().ToString());
}
```


