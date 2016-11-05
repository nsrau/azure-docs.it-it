## Peering tra sottoscrizioni
In questo scenario si creerà un peering tra due reti virtuali appartenenti a sottoscrizioni diverse.

![Scenario tra sottoscrizioni](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

Il peering reti virtuali si basa sul controllo degli accessi in base al ruolo per l'autorizzazione. Per lo scenario tra sottoscrizioni , prima è necessario concedere un'autorizzazione sufficiente agli utenti che creeranno il collegamento per il peering:

> [!NOTE]
> Se lo stesso utente ha il privilegio per entrambe le sottoscrizioni, è possibile saltare i passaggi da 1 a 4 seguenti.
> 
> 

<!---HONumber=AcomDC_0921_2016-->