## <a name="a-namex-subapeering-across-subscriptions"></a><a name="x-sub"></a>Peering tra sottoscrizioni
In questo scenario si creerà un peering tra due reti virtuali appartenenti a sottoscrizioni diverse.

![Scenario tra sottoscrizioni](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

Il peering reti virtuali si basa sul controllo degli accessi in base al ruolo per l'autorizzazione. Per lo scenario tra sottoscrizioni è prima necessario concedere un'autorizzazione sufficiente agli utenti che creeranno il collegamento per il peering.

> [!NOTE]
> Se lo stesso utente ha il privilegio per entrambe le sottoscrizioni, è possibile saltare i passaggi 1-4 seguenti.
> 
> 



<!--HONumber=Feb17_HO1-->


