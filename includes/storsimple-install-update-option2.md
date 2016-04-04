<!--author=SharS last changed: 03/17/2016-->

#### Per installare l'aggiornamento 1.2 dal portale di Azure classico

1. Nel portale di Azure classico, accedere alla pagina **Dispositivi** e selezionare il proprio dispositivo.

2. Andare a **Dispositivi** > **Configura**.

3. In **interfacce di rete**, verificare innanzitutto di disporre di almeno un’interfaccia di rete abilitata per iSCSI. Poi individuare l'interfaccia di rete (diversa da DATA 0) con gateway assegnato.

4. Disabilitare l'interfaccia di rete con un gateway assegnato e salvare la configurazione modificata. Si noti che vengono mantenute le impostazioni dell'interfaccia di rete e pertanto quando si riattiva questa interfaccia di rete in un secondo momento, il portale consentirà di ripristinare le impostazioni originali.

7. A questo punto è possibile [utilizzare il portale di Azure classico per installare l'aggiornamento 1.2](#install-update-12-via-the-azure-classic-portal). Seguire le istruzioni a partire dal passaggio 3 di questa procedura. Dopo aver installato tutti gli aggiornamenti, è possibile riabilitare l'interfaccia di rete che è stata disabilitata.

<!---HONumber=AcomDC_0323_2016-->