# Running the Slicing Experiment 

****1. SSH into the O-RAN node and run the following command to obtain the IP adress of the shared VLAN that was created :**** <br />
-  Copy the IP adress to clipboard as we will require next we deploy the eNodeB
   

```shell

kubectl get svc -n ricplt --field-selector metadata.name=service-ricplt-e2term-sctp-alpha -o jsonpath='{.items[0].spec.clusterIP}'

```
****2. Next, SSH into the**** "m-nb" node or "ota-x310-comp" ****(for indoor ota experiment) and run the following**** : 

```shell
sudo srsenb
```
****3. Open an another terminal and ssh into the same node as previous and run the following :****  <br />
-  Here we deploy the ENodeB , make sure to connect it with the O-RAN by changing the variable E2TERM_IP to the IP adress obtained
   
```shell
sudo srsenb --ric.agent.remote_ipv4_addr=${E2TERM_IP} --log.all_level=warn --ric.agent.log_level=debug --log.filename=stdout

```
****4. Now we can deploy the XApp, SSH into the node-0 of the O-RAN Experiment, Follow the steps :****    <br />

<br />

   i. Onboard the nex-RAN XApp <br />
   
```shell
    /local/setup/oran/dms_cli onboard \
    /local/profile-public/nexran-config-file.json \
    /local/setup/oran/xapp-embedded-schema.json
```
<br />   
    ii. To verify : <br />
    
```shell
    /local/setup/oran/dms_cli get_charts_list
```   
<br />
    iii. Next, to deploy  <br />
    
       
```shell
    /local/setup/oran/dms_cli install \
    --xapp_chart_name=nexran --version=0.1.0 --namespace=ricxapp
```
<br />
    iv. To view the logs of nexran XApp <br />
    
```shell
    kubectl logs -f -n ricxapp -l app=ricxapp-nexran
```
    


****5. In a new ssh connection to node-0, collect the IP address of the nexran API****



```shell
. /local/repository/demo/get-env.sh

```

****6. To verify we are able to use the IP Address****
```shell
curl -i -X GET http://${NEXRAN_XAPP}:8000/v1/version ; echo ; echo
```

**7. For visualizing, confgure the Grafana Dashboard**  <br />
    - Note : To open Grafana Dasboard, see in the profile instructions of the O-RAN experiment, a unique link to open and username and password 
   
```shell


. /local/repository/demo/get-env.sh
curl -L -X PUT http://$NEXRAN_XAPP:8000/v1/appconfig \
    -H "Content-type: application/json" \
    -d '{"kpm_interval_index":18,"influxdb_url":"'$INFLUXDB_URL'?db=nexran"}'
```

**8. Now we would deploy the UE in the "m-ue" node or "ota-nuc"(for indoor experiment)**
```shell
sudo srsue
```

**9. Once UE attached, run an IPERF server at the ENodeB, i.e "m-nb" node or "ota-x310-comp" by**

```shell
iperf3 -s -B 192.168.0.1 -p 5010 -i 1

```
**10. Now run the ipert client at "m-ue" node or "ota-nuc" node**
```shell
iperf3 -c 192.168.0.1 -p 5010 -i 1 -t 36000 -R
```
**11. To perform the slicing node, open another terminal to node-0 of O-RAN experiment and run**
-  You should be able to see the bandwidth dropping in the iperf terminal
```shell
/local/repository/demo/run-nexran-slicing.sh
```

**12. For further, you can change the priority and would see the bandwidth drop more**
```shell
. /local/repository/demo/get-env.sh
curl -i -X PUT -H "Content-type: application/json" -d '{"allocation_policy":{"type":"proportional","share":1024}}' http://${NEXRAN_XAPP}:8000/v1/slices/slow ; echo ; echo ;
curl -i -X PUT -H "Content-type: application/json" -d '{"allocation_policy":{"type":"proportional","share":256}}' http://${NEXRAN_XAPP}:8000/v1/slices/fast ; echo ; echo
```
<br />

<br />

**For cleanup of the script, O-RAN (required to redeploy or re-run)** <br />

<br />
   i. To stop the script running <br />
   
```shell
/local/repository/demo/cleanup-nexran.sh
```

<br />
   ii. To run experiment again stop the EPC, ENodeB, UE by Ctrl+C and redeploy by : <br />
   
```shell
kubectl -n ricxapp rollout restart deployment ricxapp-nexran
kubectl -n ricxapp rollout restart deployment ricxapp-scp-kpimon
```


<br />
   iii. To undeploy xapp <br />
   
```shell
/local/setup/oran/dms_cli uninstall \
    nexran --version=0.1.0 --namespace=ricxapp
```
