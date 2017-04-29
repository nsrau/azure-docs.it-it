## <a name="sample-scenario"></a>Scenario di esempio
Per illustrare meglio come gestire i gruppi di sicurezza di rete, questo articolo usa lo scenario seguente.

![Scenario di una rete virtuale](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

In questo scenario si creerà un NSG per ogni subnet nella rete virtuale **TestVNet** , come illustrato di seguito: 

* **NSG-FrontEnd**. Il front-end NSG verrà applicato per il subnet *front-end* , e contiene due regole:    
  * **regola-rdp**. Questa regola consente il traffico RDP verso il subnet *front-end* .
  * **regola-web**. Questa regola consente il traffico HTTP verso il subnet *front-end* .
* **Back-end di NSG**. Il back-end NSG verrà applicato per il subnet *back-end* , e contiene due regole:    
  * **regola sql**. Questa regola consente il traffico SQL solo dal subnet *front-end* .
  * **regola-web**. Questa regola nega tutto il traffico associato ad internet proveniente dal subnet *back-end* .

La combinazione di queste regole crea uno scenario simile alla rete perimetrale, dove la subnet di back-end può solo ricevere traffico in ingresso per il traffico SQL dalla subnet front-end e non dispone dell'accesso a Internet, mentre la subnet front-end può comunicare con Internet e ricevere solo le richieste HTTP in ingresso.

Per distribuire lo scenario illustrato in precedenza, selezionare [questo collegamento](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG), fare clic su **Distribuisci in Azure**, sostituire i valori del parametro predefinito se necessario e seguire le istruzioni nel portale. Nelle istruzioni di esempio seguenti il modello è stato usato per distribuire un gruppo di risorse con nome **RG-NSG**. 

