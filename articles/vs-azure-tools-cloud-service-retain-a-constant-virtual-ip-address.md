<properties
   pageTitle="Come mantenere un indirizzo IP virtuale costante per un servizio cloud | Microsoft Azure"
   description="Informazioni su come assicurare che l'indirizzo IP virtuale (indirizzo VIP) del servizio cloud di Azure non subisca modifiche."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# Come mantenere un indirizzo IP virtuale costante per un servizio cloud

Quando si aggiorna un servizio cloud ospitato in Azure, potrebbe essere necessario assicurare che l'indirizzo IP virtuale (indirizzo VIP) del servizio non subisca modifiche. Molti servizi di gestione di dominio usano DNS (Domain Name System) per la registrazione dei nomi di dominio. DNS funziona solo se l'indirizzo VIP rimane invariato. È possibile usare la **Pubblicazione guidata** in Strumenti di Azure per assicurare che l'indirizzo VIP del servizio cloud non cambi in caso di aggiornamento. Per altre informazioni su come usare la gestione dei domini DNS per i servizi cloud, vedere [Configurazione di un nome di dominio personalizzato per un servizio cloud di Azure](./cloud-services/cloud-services-custom-domain-name.md).

## Pubblicazione di un servizio cloud senza modifica dell'indirizzo VIP

L'indirizzo VIP di un servizio cloud viene allocato alla prima distribuzione in Azure in un ambiente specifico, ad esempio l'ambiente di produzione. L'indirizzo VIP viene modificato solo se si elimina esplicitamente la distribuzione o se la si elimina implicitamente mediante il processo di aggiornamento della distribuzione. Per mantenere l'indirizzo VIP, è necessario non eliminare la distribuzione e anche assicurare che Visual Studio non elimini automaticamente la distribuzione. È possibile controllare il comportamento specificando le impostazioni di distribuzione nella **Pubblicazione guidata**, che supporta alcune opzioni di distribuzione. È possibile specificare una nuova distribuzione o una distribuzione di aggiornamento, che può essere incrementale o simultanea, ed entrambi i tipi di distribuzione di aggiornamento mantengono l'indirizzo VIP. Per definizioni di questi diversi tipi di distribuzione, vedere [Procedura guidata Pubblica l'applicazione Azure](vs-azure-tools-publish-azure-application-wizard.md). È anche possibile controllare se la distribuzione precedente di un servizio cloud viene eliminata in caso di errore. Se l'opzione non viene impostata correttamente, è possibile che l'indirizzo VIP cambi in modo imprevisto.

### Per aggiornare un servizio cloud senza modifica dell'indirizzo VIP

1. Dopo avere distribuito almeno una volta il servizio cloud, aprire il menu di scelta rapida per il nodo relativo al progetto Azure, quindi scegliere **Pubblica**. Verrà visualizzata la procedura guidata **Pubblica l'applicazione Azure**.

1. Nell'elenco di sottoscrizioni scegliere quella in cui si vuole eseguire la distribuzione, quindi fare clic su **Avanti**. Verrà visualizzata la pagina **Impostazioni** della procedura guidata.

1. Nella scheda **Impostazioni comuni** verificare che il nome del servizio cloud in cui si esegue la distribuzione e i valori per **Ambiente**, **Configurazione compilazione** e **Configurazione servizio** siano corretti.

1. Nella scheda **Impostazioni avanzate** verificare che l'account di archiviazione e l'etichetta di distribuzione siano corretti, che la casella di controllo **Elimina distribuzione in caso di errore** sia deselezionata e che la casella di controllo **Aggiornamento distribuzione** sia selezionata. Se si seleziona la casella di controllo **Aggiornamento distribuzione**, si assicura che la distribuzione non verrà eliminata e che l'indirizzo VIP non andrà perso quando si ripubblica l'applicazione. Se si deseleziona la casella di controllo **Elimina distribuzione in caso di errore**, si assicura che l'indirizzo VIP non andrà perso in caso di errore durante la distribuzione.

1. Per specificare ulteriormente la modalità di aggiornamento per i ruoli, scegliere il collegamento **Impostazioni** accanto alla casella di controllo **Aggiornamento distribuzione**, quindi scegliere l'opzione relativa all'aggiornamento incrementale o simultaneo nella finestra di dialogo **Aggiornamento distribuzione**. Se si sceglie l'aggiornamento incrementale, le istanze vengono aggiornate una dopo l'altra, in modo che l'applicazione sia sempre disponibile. Se si sceglie l'aggiornamento simultaneo, tutte le istanze vengono aggiornate contemporaneamente. L'aggiornamento simultaneo è più veloce, ma è possibile che il servizio non sia disponibile durante il processo di aggiornamento.

1. Quando le impostazioni sono corrette, scegliere **Avanti**.

1. Nella pagina **Riepilogo** della procedura guidata verificare le impostazioni, quindi fare clic su **Pubblica**.

  >[AZURE.WARNING] Se la distribuzione ha esito negativo, è consigliabile risolvere i problemi ed eseguire rapidamente una nuova distribuzione, per evitare di lasciare il servizio cloud con stato danneggiato.

## Passaggi successivi

Per altre informazioni sulla pubblicazione in Azure da Visual Studio, vedere [Procedura guidata Pubblica l'applicazione Azure](vs-azure-tools-publish-azure-application-wizard.md).

<!---HONumber=AcomDC_0817_2016-->