## Peering tra sottoscrizioni

In questo scenario si creerà un peering tra due reti virtuali appartenenti a sottoscrizioni diverse.

![Scenario tra sottoscrizioni](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

Il peering reti virtuali si basa sul controllo degli accessi in base al ruolo per l'autorizzazione. Per uno scenario con collegamenti tra sottoscrizioni, è necessario concedere prima di tutto autorizzazioni sufficienti agli utenti che creeranno il collegamento per il peering. NOTA: se allo stesso utente sono concessi privilegi per entrambe le sottoscrizioni, è possibile ignorare i passaggi da 1 a 4 seguenti.

<!---HONumber=AcomDC_0803_2016-->