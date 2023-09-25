# DSSBuf

To begin, instantiate the Powder Experiment O-RAN: [Link](https://www.powderwireless.net/show-profile.php?profile=30d10b78-f939-11ea-b1eb-e4434b2381fc)

1. Make sure the Parameters are selected as below: <br />
      - Create a shared VLAN through the "Add Shared VLAN" settings with any name, make sure you take note of it.
      - Can select hardware node as 740 or 710 (preferred 740 as 710 may not be available sometimes)

<br />

 ![alt text](https://github.com/tahenan/DSSBuf/blob/main/photos/man1.jpg)

<br />


2. Once the experiment is completely initiated (takes around 30 min), 
<br /> start another experiment "srslte-shvlan-oran" :   [Link](https://www.powderwireless.net/show-profile.php?profile=1bd62bf4-5b60-11eb-b1eb-e4434b2381fc) ; 
<br /> This will act as the second experiment where the ENodeB and UE will be instantiated 

i. Attenuator Matrix Experiment <br />
      To connect the ORAN with the experiment of the ENodeB and UE connected with the attenuator matrix as shown in the diagram :

<br />


![alt text](https://github.com/tahenan/DSSBuf/blob/main/photos/man3.jpg)

- To connect to the attenuator matrix, when instantiating the experiment, in the parameters settings :-
      -  add a controlled RF resource
      -  and connect the previous experiment by adding the VLAN name in the "shared vlan name" as shown below :

![alt text](https://github.com/tahenan/DSSBuf/blob/main/photos/man2.jpg)


```
function test() {
  console.log("notice the blank line before this function?");
}
```

hello 
