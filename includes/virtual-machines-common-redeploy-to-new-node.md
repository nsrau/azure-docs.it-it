## Uso del portale di Azure

1. Selezionare la VM di cui si desidera eseguire di nuovo la distribuzione, quindi fare clic sul pulsante "Ridistribuisci" nel pannello "Impostazioni". Scorrere fino alla sezione **Supporto e risoluzione dei problemi** che contiene il pulsante "Ridistribuisci", come nell'esempio seguente:

	![Pannello VM di Azure](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)  

2. Fare clic sul pulsante "Ridistribuisci" per confermare l'operazione:

	![Pannello Ridistribuire una VM](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)  

3. Lo **stato** della VM passa ad *Aggiornamento in corso* mentre la VM si prepara per la ridistribuzione, come nell'esempio seguente:

	![Aggiornamento di una VM](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)  

4. Lo **stato** passa poi ad *Avvio in corso* mentre la VM si avvia su un nuovo host Azure, come nell'esempio seguente:

	![Avvio di una VM](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)  

5. Al termine del processo di avvio della VM, lo **stato** torna a *In esecuzione*, a indicare che la VM è stata ridistribuita:

	![Esecuzione di una VM](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)  

<!---HONumber=AcomDC_0921_2016-->