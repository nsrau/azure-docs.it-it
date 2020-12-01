---
title: Onboarding come partner di griglia di eventi di Azure usando portale di Azure
description: Usare portale di Azure per l'onboarding di un partner di griglia di eventi di Azure.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 8344fcd822bfcdc67e2c27b200d97fa70dfefdf2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349387"
---
# <a name="onboard-as-an-azure-event-grid-partner-using-the-azure-portal"></a>Onboarding come partner di griglia di eventi di Azure usando il portale di Azure
Questo articolo descrive il modo in cui i provider SaaS di terze parti, noti anche come autori di eventi o partner, vengono caricati in griglia di eventi per poter pubblicare eventi dai rispettivi servizi e come tali eventi vengono utilizzati dagli utenti finali.

> [!IMPORTANT]
> Se non si ha familiarità con gli eventi dei partner, vedere [Cenni preliminari sugli eventi partner](partner-events-overview.md) per un'introduzione dettagliata di concetti chiave fondamentali per comprendere e seguire i passaggi descritti in questo articolo.

## <a name="onboarding-process-for-event-publishers-partners"></a>Processo di onboarding per autori di eventi (partner)
In breve, consentire agli utenti di utilizzare gli eventi del servizio in genere comporta il processo seguente:

1. Prima di procedere con i passaggi successivi, **comunicare il proprio interesse** per diventare partner del team di servizio di griglia di eventi.
1. Creare un tipo di argomento partner creando una **registrazione**. 
1. Creare uno **spazio dei nomi**.
1. Creare un **canale di eventi** e un **argomento del partner** (passaggio singolo).
1. Testare la funzionalità eventi partner end-to-end.

Per il passaggio #4, è necessario decidere quale tipo di esperienza utente si desidera fornire. Sono disponibili le seguenti opzioni:
- Fornire una soluzione personalizzata, in genere un'esperienza GUI (Web Graphical User Interface), ospitata nel dominio usando l'SDK e/o l'API REST per creare un canale di eventi e il corrispondente argomento del partner. Con questa opzione è possibile chiedere all'utente la sottoscrizione e il gruppo di risorse in cui verrà creato un argomento del partner.
- Usare portale di Azure o l'interfaccia della riga di comando per creare il canale di eventi e l'argomento partner associato. Con questa opzione, è necessario avere a disposizione la sottoscrizione di Azure dell'utente in un certo modo e un gruppo di risorse in cui verrà creato un argomento del partner. 

Questo articolo illustra come eseguire l'onboarding come partner di griglia di eventi di Azure usando il portale di Azure. 

> [!NOTE]
> La registrazione di un tipo di argomento partner è un passaggio facoltativo. Per decidere se è necessario creare un tipo di argomento partner, vedere [risorse gestite da Publisher di eventi](partner-events-overview.md#resources-managed-by-event-publishers).

## <a name="communicate-your-interest-in-becoming-a-partner"></a>Comunicare il proprio interesse per diventare partner
Compilare [questo modulo](https://aka.ms/gridpartnerform) e contattare il team di griglia di eventi all'indirizzo [GridPartner@microsoft.com](mailto:GridPartner@microsoft.com) . Si riceverà una conversazione con l'utente che fornisce informazioni dettagliate sui casi d'uso degli eventi dei partner, sulle persone, sul processo di onboarding, sulle funzionalità, sui prezzi e altro ancora.

## <a name="prerequisites"></a>Prerequisiti
Per completare i passaggi rimanenti, verificare di avere:

- Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Un [tenant](../active-directory/develop/quickstart-create-new-tenant.md)di Azure.

## <a name="register-a-partner-topic-type-optional"></a>Registrare un tipo di argomento partner (facoltativo)
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **tutti i servizi** nel riquadro di spostamento a sinistra, quindi digitare le **registrazioni dei partner di griglia di eventi** nella barra di ricerca e selezionarlo. 
1. Nella pagina **registrazioni partner di griglia di eventi** selezionare **+ Aggiungi** sulla barra degli strumenti. 

    :::image type="content" source="./media/onboard-partner/add-partner-registration-link.png" alt-text="Aggiungi collegamento per la registrazione partner":::
1. Nella pagina **Crea argomento partner digitare registrations-nozioni di base** immettere le informazioni seguenti: 
    1. Nella sezione **Dettagli progetto** , attenersi alla seguente procedura:
        1. Selezionare la **sottoscrizione** di Azure. 
        1. Selezionare un gruppo di **risorse** di Azure esistente o creare un nuovo gruppo di risorse. 
    1. Nella sezione **Dettagli registrazione** seguire questa procedura:
        1. Per **nome registrazione** immettere un nome per la registrazione. 
        1. Per **nome organizzazione** immettere il nome dell'organizzazione. 
    1. Nella sezione **tipo di risorsa partner** immettere i dettagli sul tipo di risorsa che verrà visualizzato nella pagina di **creazione dell'argomento partner** : 
        1. Per **nome del tipo di risorsa partner** immettere il nome del tipo di risorsa. Si tratta del tipo di argomento del partner che verrà creato nella sottoscrizione di Azure. 
        2. Per **nome visualizzato** immettere un nome visualizzato descrittivo per il tipo di argomento partner (risorsa). 
        3. Immettere una **Descrizione per il tipo di risorsa**. 
        4. Immettere una **Descrizione per lo scenario**. Deve spiegare i modi o gli scenari in cui è possibile usare gli argomenti dei partner per le risorse.  

            :::image type="content" source="./media/onboard-partner/create-partner-registration-page.png" alt-text="Crea registrazione partner":::            
1. Selezionare **Avanti: servizio personalizzato** nella parte inferiore della pagina. Nella scheda **Customer Service** della pagina **create partner Registration** immettere le informazioni che gli utenti del Sottoscrittore utilizzeranno per contattare l'utente in caso di problemi con l'origine evento:
    1. Immettere il **numero di telefono**.
    1. Immettere l' **estensione** per il numero di telefono.
    1. Immettere un **URL** del sito Web di supporto. 
    
        :::image type="content" source="./media/onboard-partner/create-partner-registration-customer-service.png" alt-text="Crea registrazione partner-servizio clienti":::        
1. Selezionare **Avanti: Tag** nella parte inferiore della pagina. 
1. Nella pagina **tag** configurare i valori seguenti. 
    1. Immettere un **nome** e un **valore** per il tag da aggiungere. Questo passaggio è **facoltativo**. 
    1. Selezionare **Verifica + crea** nella parte inferiore della pagina per creare la registrazione (tipo di argomento partner).

## <a name="create-a-partner-namespace"></a>Creare uno spazio dei nomi partner

1. Nella portale di Azure selezionare **tutti i servizi** dal menu di spostamento a sinistra, quindi digitare gli **spazi dei nomi del partner griglia di eventi** nella barra di ricerca e quindi selezionarlo dall'elenco. 
1. Nella pagina **spazi dei nomi partner di griglia di eventi** selezionare **+ Aggiungi** sulla barra degli strumenti. 
    
    :::image type="content" source="./media/onboard-partner/add-partner-namespace-link.png" alt-text="Spazi dei nomi partner-Aggiungi collegamento":::
1. Nella pagina **Crea spazio dei nomi partner-nozioni di base** specificare le informazioni seguenti.
    1. Nella sezione **Project Details** seguire questa procedura: 
        1. Selezionare una **sottoscrizione** di Azure.
        1. Selezionare un **gruppo di risorse** esistente o creare un gruppo di risorse. 
    1. Nella sezione **Dettagli spazio dei nomi** , attenersi alla procedura seguente:
        1. Immettere un **nome** per lo spazio dei nomi. 
        1. Selezionare una **località** per lo spazio dei nomi. 
    1. Nella sezione **Dettagli registrazione** seguire questa procedura per associare lo spazio dei nomi a una registrazione partner. 
        1. Selezionare la **sottoscrizione** in cui esiste la registrazione del partner. 
        1. Selezionare il **gruppo di risorse** che contiene la registrazione del partner. 
        1. Selezionare la **registrazione partner** dall'elenco a discesa.
    1. Selezionare **Avanti: Tag** nella parte inferiore della pagina.

        :::image type="content" source="./media/onboard-partner/create-partner-namespace-basics-page.png" alt-text="Crea spazio dei nomi partner-pagina Nozioni di base":::
1. Nella pagina **tag** aggiungere i tag (facoltativo).
    1. Immettere un **nome** e un **valore** per il tag da aggiungere. Questo passaggio è **facoltativo**.
    1. Selezionare **Rivedi e crea** nella parte inferiore della pagina.         
1. Nella pagina **Verifica e crea** esaminare i dettagli e selezionare **Crea**. 

## <a name="create-an-event-channel"></a>Creare un canale di eventi
> [!IMPORTANT]
> È necessario richiedere all'utente una sottoscrizione, un gruppo di risorse, un percorso e un nome di argomento partner di Azure per creare un argomento partner che l'utente sarà proprietario e gestirà.

1. Passare alla pagina **Panoramica** dello spazio dei nomi creato. 

    :::image type="content" source="./media/onboard-partner/partner-namespace-overview.png" alt-text="Spazio dei nomi partner-pagina Panoramica":::
    partner-namespace-overview.png
1. Selezionare **+ canale eventi** sulla barra degli strumenti. 
1. Nella pagina **crea canale evento-nozioni di base** specificare le informazioni seguenti. 
    1. Nella sezione **Dettagli canale** eseguire le operazioni seguenti:
        1. Per **nome canale evento** immettere un nome per il canale dell'evento. 
        1. Immettere l' **origine**. Vedere le [specifiche degli eventi Cloud 1,0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#source-1) per ottenere un'idea di un valore appropriato per l'origine. Vedere anche [questo esempio di schema di eventi Cloud](cloud-event-schema.md#sample-event-using-cloudevents-schema).
        1. Immettere l'origine (cosa è?).
    1. Nella sezione **Dettagli destinazione** immettere i dettagli per l'argomento partner di destinazione che verrà creato per questo canale di eventi. 
        1. Immettere l' **ID della sottoscrizione** in cui verrà creato l'argomento del partner. 
        1. Immettere il **nome del gruppo di risorse** in cui verrà creata la risorsa dell'argomento partner. 
        1. Immettere un **nome per l'argomento partner**. 
    1. Selezionare **Avanti: filtri** nella parte inferiore della pagina. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-basics-page.png" alt-text="Crea canale evento-pagina Nozioni di base":::
1. Nella pagina **filtri** aggiungere i filtri. seguire anche questa procedura:
    1. Filtrare gli attributi di ogni evento. Vengono recapitati solo gli eventi che corrispondono a tutti i filtri. È possibile specificare un massimo di 25 filtri. I confronti non fanno distinzione tra maiuscole e minuscole. Le chiavi valide utilizzate per i filtri variano in base allo schema dell'evento. Nell'esempio seguente,, `eventid` , `source` `eventtype` e `eventtypeversioin` possono essere usati per le chiavi. È inoltre possibile utilizzare le proprietà personalizzate all'interno del payload dei dati, utilizzando `.` come operatore di annidamento. Ad esempio: `data` , `data.key` , `data.key1.key2` .
    1. Selezionare **Avanti: funzionalità aggiuntive** nella parte inferiore della pagina. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-filters-page.png" alt-text="Crea canale evento-pagina filtri":::
        create-event-channel-filters-page.png
1. Nella pagina **funzionalità aggiuntive** è possibile impostare un'ora di **scadenza** e una **Descrizione per l'argomento partner**. 
    1. L' **ora di scadenza** è l'ora in cui l'argomento e il canale di eventi associato verranno eliminati automaticamente se non vengono attivati dal cliente. Viene usato un valore predefinito di sette giorni nel caso in cui non venga fornito un orario. Selezionare la casella di controllo per specificare la data di scadenza. 
    1. Poiché questo argomento è una risorsa che non viene creata dall'utente, una **Descrizione** può aiutare gli utenti a comprendere la natura di questo argomento. Se non è impostato alcun valore, verrà fornita una descrizione generica. Selezionare la casella di controllo per impostare la descrizione dell'argomento del partner. 
    1. Selezionare **Avanti: Rivedi e crea**. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-additional-features-page.png" alt-text="Crea canale evento-pagina funzionalità aggiuntive":::
1. In **Verifica e crea** rivedere le impostazioni e selezionare **Crea** per creare il canale dell'evento. 

## <a name="next-steps"></a>Passaggi successivi
- [Panoramica degli argomenti partner](./partner-events-overview.md)
- [Modulo di onboarding degli argomenti del partner](https://aka.ms/gridpartnerform)
- [Argomento del partner Auth0](auth0-overview.md)
- [Come usare l'argomento partner Auth0](auth0-how-to.md)