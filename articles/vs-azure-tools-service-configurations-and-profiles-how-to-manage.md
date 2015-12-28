<properties
   pageTitle="Come gestire le configurazioni e i profili dei servizi | Microsoft Azure"
   description="Informazioni su come usare i file di configurazione del servizio e di configurazione dei profili che archiviano le impostazioni per gli ambienti di distribuzione e le impostazioni di pubblicazione per i servizi cloud."
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
   ms.date="08/13/2015"
   ms.author="tarcher" />

# Come gestire le configurazioni e i profili dei servizi

## Panoramica

Quando si pubblica un servizio cloud, Visual Studio archivia le informazioni di configurazione in due tipi di file di configurazione, ovvero le configurazioni e i profili del servizio. Le configurazioni del servizio (file con estensione cscfg) archiviano le impostazioni per gli ambienti di distribuzione per un servizio cloud di Azure. Azure usa questi file di configurazione quando gestisce i servizi cloud. I profili (file con estensione azurePubxml) invece archiviano le impostazioni di pubblicazione per i servizi cloud. Queste impostazioni sono un record delle opzioni selezionate durante la pubblicazione guidata e vengono usate localmente da Visual Studio. Questo argomento illustra come usare entrambi i tipi di file di configurazione.

## Configurazioni del servizio

È possibile creare più configurazioni del servizio da usare per ogni ambiente di distribuzione. Ad esempio, è possibile creare una configurazione del servizio per l'ambiente locale da usare per eseguire e testare un'applicazione Azure e un'altra configurazione del servizio per l'ambiente di produzione.

È possibile aggiungere, eliminare, rinominare e modificare queste configurazioni del servizio in base alle proprie esigenze. È possibile gestire queste configurazioni del servizio da Visual Studio, come indicato nella figura seguente.

![Gestisci configurazioni di servizio](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

È inoltre possibile aprire la finestra di dialogo **Gestisci configurazioni** dalle pagine delle proprietà del ruolo. Per accedere alle proprietà per un ruolo nel progetto Azure, aprire il menu di scelta rapida per il ruolo in questione, quindi scegliere **Proprietà**. Nella scheda **Impostazioni** espandere l'elenco **Configurazione del servizio**, quindi fare clic su **Gestisci** per aprire la finestra di dialogo **Gestisci configurazioni**.

### Per aggiungere una configurazione del servizio

1. In Esplora soluzioni aprire il menu di scelta rapida per il progetto Azure e fare clic su **Gestisci configurazioni**.

    Verrà visualizzata la finestra di dialogo **Gestisci configurazioni di servizio**.

1. Per aggiungere una configurazione del servizio, è necessario creare una copia di una configurazione esistente. Per eseguire questa operazione, scegliere la configurazione da copiare dall'elenco Nome, quindi fare clic su **Crea copia**.

1. (Facoltativo) Per dare alla configurazione del servizio un nome diverso, scegliere la nuova configurazione del servizio dall'elenco Nome, quindi fare clic su **Rinomina**. Nella casella di testo **Nome** digitare il nome da usare per questa configurazione del servizio, quindi fare clic su **OK**.

    Un nuovo file di configurazione del servizio denominato ServiceConfiguration.<New Name>.cscfg viene aggiunto a un progetto Azure in Esplora soluzioni.


### Per eliminare una configurazione del servizio

1. In Esplora soluzioni aprire il menu di scelta rapida per il progetto Azure e fare clic su **Gestisci configurazioni**.

    Verrà visualizzata la finestra di dialogo **Gestisci configurazioni di servizio**.

1. Per eliminare una configurazione del servizio, selezionarla dall'elenco **Nome**, quindi fare clic su **Rimuovi**. Verrà visualizzata una finestra di dialogo per verificare se si vuole eliminare questa configurazione.

1. Fare clic su **Elimina**.

     Il file di configurazione del servizio viene rimosso dal progetto Azure in Esplora soluzioni.


### Per rinominare una configurazione del servizio

1. In Esplora soluzioni aprire il menu di scelta rapida per il progetto Azure e fare clic su **Gestisci configurazioni**.

    Verrà visualizzata la finestra di dialogo **Gestisci configurazioni di servizio**.

1. Per rinominare una configurazione del servizio, scegliere la nuova configurazione del servizio dall'elenco **Nome**, quindi fare clic su **Rinomina**. Nella casella di testo **Nome** digitare il nome da usare per questa configurazione del servizio, quindi fare clic su **OK**.

    Il nome del file di configurazione del servizio viene modificato nel progetto Azure in Esplora soluzioni.

### Per modificare una configurazione del servizio

- Se si vuole modificare una configurazione del servizio, aprire il menu di scelta rapida di un ruolo specifico da modificare in un progetto Azure, quindi fare clic su Proprietà. Per altre informazioni, vedere [Procedura: Configurare i ruoli di un servizio cloud di Azure con Visual Studio](https://msdn.microsoft.com/library/azure/hh369931.aspx).

## Effettuare diverse combinazioni di impostazioni usando i profili

Mediante i profili è possibile popolare automaticamente la **Pubblicazione guidata** con diverse combinazioni di impostazioni usate per scopi differenti. Ad esempio, è possibile definire un profilo per eseguire il debug e uno per le build di rilascio. In questo caso, nel profilo **Debug** sarà abilitato **IntelliTrace** e sarà selezionata la configurazione **Debug**, mentre nel profilo **Rilascio** sarà disabilitato **IntelliTrace** e sarà selezionata la configurazione **Rilascio**. È anche possibile usare diversi profili per distribuire un servizio mediante un account di archiviazione diverso.

Quando si esegue la procedura guidata per la prima volta, viene creato un profilo predefinito. Visual Studio consente di archiviare il profilo in un file con estensione azurePubXml, aggiunto al progetto Azure nella cartella **Profili**. Se si specificano manualmente opzioni diverse nelle successive esecuzioni della procedura guidata, il file viene aggiornato automaticamente. Prima di eseguire la procedura riportata di seguito, è necessario avere già pubblicato almeno una volta il servizio cloud.

### Per aggiungere un profilo

1. Aprire il menu di scelta rapida per il progetto Azure, quindi scegliere **Pubblica**.

1. Accanto al **Profilo di destinazione** fare clic su **Salva profilo**, come illustrato nella figura seguente. Verrà creato un profilo per l'utente.

    ![Creare un nuovo profilo](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)

1. Dopo aver creato il profilo, fare clic su **<Gestisci…>** nell'elenco **Profilo di destinazione**.

    Viene visualizzata la finestra di dialogo **Gestione profili**, come illustrato nella figura seguente.

    ![Finestra di dialogo Gestione profili](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)

1. Nell'elenco **Nome** scegliere un profilo, quindi **Crea copia**.

1. Fare clic su **Chiudi**.

    Il nuovo profilo viene visualizzato nell'elenco Profilo di destinazione.

1. Nell'elenco **Profilo di destinazione** fare clic sul profilo appena creato. Le impostazioni della pubblicazione guidata vengono popolate in base alle opzioni relative al profilo selezionato.

1. Fare clic sui pulsanti **Indietro** e **Avanti** per visualizzare ogni pagina della pubblicazione guidata, quindi personalizzare le impostazioni per il profilo. Per altre informazioni, vedere [Procedura guidata Pubblica l'applicazione Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085).

1. Dopo avere personalizzato le impostazioni, fare clic su **Avanti** per tornare alla pagina Impostazioni. Il profilo viene salvato quando si pubblica il servizio con queste impostazioni o se si fa clic su **Salva** accanto all'elenco dei profili.

### Per rinominare o eliminare un profilo

1. Aprire il menu di scelta rapida per il progetto Azure, quindi scegliere **Pubblica**.

1. Nell'elenco **Profilo di destinazione** fare clic su **Gestisci**.

1. Nella finestra di dialogo **Gestione profili** fare clic sul profilo da eliminare, quindi su **Rimuovi**.

1. Nella finestra di dialogo di conferma visualizzata fare clic su **OK**.

1. Fare clic su **Close**.

### Per modificare un profilo

1. Aprire il menu di scelta rapida per il progetto Azure, quindi scegliere **Pubblica**.

1. Nell'elenco **Profilo di destinazione** fare clic sul profilo da modificare.

1. Fare clic sui pulsanti **Indietro** e **Avanti** per visualizzare ogni pagina della pubblicazione guidata, quindi modificare le impostazioni desiderate. Per altre informazioni, vedere [Procedura guidata Pubblica l'applicazione Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085).

1. Dopo avere modificato le impostazioni, fare clic su **Avanti** per tornare alla pagina **Impostazioni**.

1. (Facoltativo) Fare clic su **Pubblica** per pubblicare il servizio cloud usando le nuove impostazioni. Se per il momento non si vuole pubblicare il servizio cloud e si chiude la pubblicazione guidata, Visual Studio richiede se si vogliono salvare le modifiche apportate al profilo.

## Passaggi successivi

Per informazioni sulla configurazione di altre parti del progetto Azure da Visual Studio, vedere [Configurazione di un progetto Azure](http://go.microsoft.com/fwlink/p/?LinkID=623075)

<!---HONumber=AcomDC_1217_2015-->