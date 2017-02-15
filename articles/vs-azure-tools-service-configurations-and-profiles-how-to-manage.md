---
title: Come gestire le configurazioni e i profili dei servizi |Documentazione Microsoft
description: Informazioni su come usare i file di configurazione del servizio e di configurazione dei profili che archiviano le impostazioni per gli ambienti di distribuzione e le impostazioni di pubblicazione per i servizi cloud.
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 7da8c551-fb06-4057-b5c7-c77f4b39d803
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c83d779797493f6ec48d61563aa7448db7c9b917


---
# <a name="how-to-manage-service-configurations-and-profiles"></a>Come gestire le configurazioni e i profili dei servizi
## <a name="overview"></a>Overview
Quando si pubblica un servizio cloud, Visual Studio archivia le informazioni di configurazione in due tipi di file di configurazione, ovvero le configurazioni e i profili del servizio. Le configurazioni del servizio (file con estensione cscfg) archiviano le impostazioni per gli ambienti di distribuzione per un servizio cloud di Azure. Azure usa questi file di configurazione quando gestisce i servizi cloud. I profili (file con estensione azurePubxml) invece archiviano le impostazioni di pubblicazione per i servizi cloud. Queste impostazioni sono un record delle opzioni selezionate durante la pubblicazione guidata e vengono usate localmente da Visual Studio. Questo argomento illustra come usare entrambi i tipi di file di configurazione.

## <a name="service-configurations"></a>Configurazioni del servizio
È possibile creare più configurazioni del servizio da usare per ogni ambiente di distribuzione. Ad esempio, è possibile creare una configurazione del servizio per l'ambiente locale da usare per eseguire e testare un'applicazione Azure e un'altra configurazione del servizio per l'ambiente di produzione.

È possibile aggiungere, eliminare, rinominare e modificare queste configurazioni del servizio in base alle proprie esigenze. È possibile gestire queste configurazioni del servizio da Visual Studio, come indicato nella figura seguente.

![Gestisci configurazioni di servizio](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

È inoltre possibile aprire la finestra di dialogo **Gestisci configurazioni** dalle pagine delle proprietà del ruolo. Per accedere alle proprietà per un ruolo nel progetto Azure, aprire il menu di scelta rapida per il ruolo in questione, quindi scegliere **Proprietà**. Nella scheda **Impostazioni** espandere l'elenco **Configurazione del servizio**, quindi fare clic su **Gestisci** per aprire la finestra di dialogo **Gestisci configurazioni**.

### <a name="to-add-a-service-configuration"></a>Per aggiungere una configurazione del servizio
1. In Esplora soluzioni aprire il menu di scelta rapida per il progetto Azure e selezionare **Gestisci configurazioni**.
   
    Verrà visualizzata la finestra di dialogo **Gestisci configurazioni di servizio** .
2. Per aggiungere una configurazione del servizio, è necessario creare una copia di una configurazione esistente. Per eseguire questa operazione, scegliere la configurazione da copiare dall'elenco Nome, quindi selezionare **Crea copia**.
3. (Facoltativo) Per rinominare una configurazione del servizio, scegliere la nuova configurazione del servizio dall'elenco Nome, quindi selezionare **Rinomina**. Nella casella di testo **Nome** digitare il nome da usare per questa configurazione del servizio, quindi selezionare **OK**.
   
    Un nuovo file di configurazione del servizio denominato ServiceConfiguration.[New Name].cscfg viene aggiunto a un progetto Azure in Esplora soluzioni.

### <a name="to-delete-a-service-configuration"></a>Per eliminare una configurazione del servizio
1. In Esplora soluzioni aprire il menu di scelta rapida per il progetto Azure e selezionare **Gestisci configurazioni**.
   
    Verrà visualizzata la finestra di dialogo **Gestisci configurazioni di servizio** .
2. Per eliminare una configurazione del servizio, selezionarla dall'elenco **Nome**, quindi scegliere **Rimuovi**. Verrà visualizzata una finestra di dialogo per verificare se si vuole eliminare questa configurazione.
3. Selezionare **Elimina**.
   
     Il file di configurazione del servizio viene rimosso dal progetto Azure in Esplora soluzioni.

### <a name="to-rename-a-service-configuration"></a>Per rinominare una configurazione del servizio
1. In Esplora soluzioni aprire il menu di scelta rapida per il progetto Azure e selezionare **Gestisci configurazioni**.
   
    Verrà visualizzata la finestra di dialogo **Gestisci configurazioni di servizio** .
2. Per rinominare una configurazione del servizio, selezionarla dall'elenco **Nome**, quindi scegliere **Rinomina**. Nella casella di testo **Nome** digitare il nome da usare per questa configurazione del servizio, quindi selezionare **OK**.
   
    Il nome del file di configurazione del servizio viene modificato nel progetto Azure in Esplora soluzioni.

### <a name="to-change-a-service-configuration"></a>Per modificare una configurazione del servizio
* Se si vuole modificare una configurazione del servizio, aprire il menu di scelta rapida di un ruolo specifico da modificare in un progetto Azure, quindi selezionare **Proprietà**. Per altre informazioni, vedere [Procedura: configurare i ruoli di un servizio cloud di Azure con Visual Studio](https://msdn.microsoft.com/library/azure/hh369931.aspx) .

## <a name="make-different-setting-combinations-by-using-profiles"></a>Effettuare diverse combinazioni di impostazioni usando i profili
Mediante i profili è possibile popolare automaticamente la **Pubblicazione guidata** con diverse combinazioni di impostazioni usate per scopi differenti. Ad esempio, è possibile definire un profilo per eseguire il debug e uno per le build di rilascio. In questo caso, nel profilo **Debug** sarà abilitato **IntelliTrace** e sarà selezionata la configurazione **Debug**, mentre nel profilo **Rilascio** sarà disabilitato **IntelliTrace** e sarà selezionata la configurazione **Rilascio**. È anche possibile usare diversi profili per distribuire un servizio mediante un account di archiviazione diverso.

Quando si esegue la procedura guidata per la prima volta, viene creato un profilo predefinito. Visual Studio consente di archiviare il profilo in un file con estensione azurePubXml, aggiunto al progetto Azure nella cartella **Profili** . Se si specificano manualmente opzioni diverse nelle successive esecuzioni della procedura guidata, il file viene aggiornato automaticamente. Prima di eseguire la procedura riportata di seguito, è necessario avere già pubblicato almeno una volta il servizio cloud.

### <a name="to-add-a-profile"></a>Per aggiungere un profilo
1. Aprire il menu di scelta rapida per il progetto Azure e quindi selezionare **Pubblica**.
2. Accanto al **Profilo di destinazione**, selezionare il pulsante **Salva profilo**, come illustrato nella figura seguente. Verrà creato un profilo per l'utente.
   
    ![Creare un nuovo profilo](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)
3. Dopo aver creato il profilo, selezionare **<Gestisci…>** nell'elenco **Profilo di destinazione**.
   
    Viene visualizzata la finestra di dialogo **Gestione profili** , come illustrato nella figura seguente.
   
    ![Finestra di dialogo Gestione profili](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)
4. Nell'elenco **Nome** scegliere un profilo, quindi selezionare **Crea copia**.
5. Fare clic su **Chiudi** .
   
    Il nuovo profilo viene visualizzato nell'elenco Profilo di destinazione.
6. Nell'elenco **Profilo di destinazione** , selezionare il profilo appena creato. Le impostazioni della pubblicazione guidata vengono popolate in base alle opzioni relative al profilo selezionato.
7. Selezionare i pulsanti **Indietro** e **Avanti** per visualizzare ogni pagina della pubblicazione guidata, quindi personalizzare le impostazioni per il profilo. Per altre informazioni, vedere [Procedura guidata per la pubblicazione dell'applicazione Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) .
8. Dopo avere personalizzato le impostazioni, selezionare **Avanti** per tornare alla pagina Impostazioni. Il profilo viene salvato quando si pubblica il servizio con queste impostazioni o se si seleziona **Salva** accanto all'elenco dei profili.

### <a name="to-rename-or-delete-a-profile"></a>Per rinominare o eliminare un profilo
1. Aprire il menu di scelta rapida per il progetto Azure e quindi selezionare **Pubblica**.
2. Nell'elenco **Profilo di destinazione** selezionare **Gestisci**.
3. Nella finestra di dialogo **Gestione profili** selezionare profilo da eliminare, quindi scegliere **Rimuovi**.
4. Nella finestra di dialogo di conferma visualizzata, selezionare **OK**.
5. Selezionare **Chiudi**.

### <a name="to-change-a-profile"></a>Per modificare un profilo
1. Aprire il menu di scelta rapida per il progetto Azure e quindi selezionare **Pubblica**.
2. Nell'elenco **Profilo di destinazione** , selezionare il profilo da modificare.
3. Selezionare i pulsanti **Indietro** e **Avanti** per visualizzare ogni pagina della pubblicazione guidata, quindi modificare le impostazioni desiderate. Per altre informazioni, vedere [Procedura guidata per la pubblicazione dell'applicazione Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) .
4. Dopo avere modificato le impostazioni, selezionare **Avanti** per tornare alla pagina **Impostazioni**.
5. (Facoltativo) Selezionare **Pubblica** per pubblicare il servizio cloud usando le nuove impostazioni. Se per il momento non si vuole pubblicare il servizio cloud e si chiude la pubblicazione guidata, Visual Studio richiede se si vogliono salvare le modifiche apportate al profilo.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulla configurazione di altre parti del progetto Azure da Visual Studio, vedere [Configurazione di un progetto Azure](http://go.microsoft.com/fwlink/p/?LinkID=623075)




<!--HONumber=Nov16_HO3-->


