## <a name="scenario"></a>Scenario
Per illustrare meglio come creare un UDR, in questo documento verrà utilizzato lo scenario seguente.

![DESCRIZIONE DELL’IMMAGINE](./media/virtual-network-create-udr-scenario-include/figure1.png)

In questo scenario si creerà un UDR per la *subnet front-end* e un altro UDR per la *subnet back-end*, come descritto di seguito: 

* **UDR-FrontEnd**. Il front-end UDR verrà applicato per il subnet *front-end* , e contiene una route:    
  * **RouteToBackend**. Questa route invia tutto il traffico nella subnet di back-end per la macchina virtuale **FW1** .
* **Back-end di UDR**. Il back-end UDR verrà applicato per il subnet *back-end* , e contiene una route:    
  * **RouteToFrontend**. Questa route invia tutto il traffico nella subnet di front-end per la macchina virtuale **FW1** .

La combinazione di queste route garantisce che tutto il traffico destinato da una subnet a un’altra venga indirizzato alla macchina virtuale **FW1** , che viene utilizzato come un dispositivo virtuale. È inoltre necessario attivare l'inoltro IP per tale macchina virtuale, per garantire che possa ricevere il traffico destinato ad altre macchine virtuali.



<!--HONumber=Nov16_HO3-->


