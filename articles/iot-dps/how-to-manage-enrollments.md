---
title: Gestire le registrazioni dei dispositivi per il servizio Device provisioning in hub Azure nel portale di Azure
description: Come gestire le registrazioni dei dispositivi per il servizio Device provisioning (DPS) nell'portale di Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: 6ec146a05df1b896f8ca594d29cf13341b70765a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954563"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Come gestire le registrazioni dei dispositivi con portale di Azure

La *registrazione dei dispositivi* crea un record di un singolo dispositivo o di un gruppo di dispositivi registrato in un dato punto con il Servizio Device Provisioning in hub IoT di Azure. Il record di registrazione contiene la configurazione iniziale per i dispositivi come parte della registrazione. Incluso nella configurazione è l'hub delle cose a cui viene assegnato un dispositivo o un criterio di allocazione che configura l'hub da un set di hub. In questo articolo viene illustrato come gestire le registrazioni dei dispositivi per il servizio di provisioning.


## <a name="create-a-device-enrollment"></a>Creare una registrazione dei dispositivi

Esistono due modi per registrare i dispositivi con il servizio di provisioning:

* Un **gruppo di registrazioni** è una voce per un gruppo di dispositivi che condividono un meccanismo di attestazione comune. Si consiglia di usare un gruppo di registrazione per un numero elevato di dispositivi che condividono una configurazione iniziale o per tutti i dispositivi che passano allo stesso tenant. Sono supportati i dispositivi che usano [chiavi simmetriche](concepts-symmetric-key-attestation.md) o [certificati X. 509](concepts-x509-attestation.md) . 

    Per istruzioni dettagliate sulla creazione e l'uso di gruppi di registrazione con chiavi simmetriche, vedere l'esercitazione effettuare il [provisioning di dispositivi con chiavi simmetriche](how-to-legacy-device-symm-key.md) .

    Si crea un gruppo di registrazione nel portale per un gruppo di dispositivi attenendosi alla procedura seguente:

    1. Accedere al portale di Azure e fare clic su **Tutte le risorse** nel menu a sinistra.  
    1. Selezionare dall'elenco di risorse il servizio di provisioning dei dispositivi per cui si desidera registrare il dispositivo.  
    1. Nel servizio di provisioning fare clic su **Gestisci registrazioni**, quindi fare clic sul pulsante **Aggiungi gruppo di registrazioni** nella parte superiore.  
     
        ![Gruppo di registrazione nel portale](./media/how-to-manage-enrollments/add-group-enrollment.png)
        
    1. Quando viene visualizzato il pannello "Aggiungi gruppo di registrazioni", immettere le informazioni per la registrazione e fare clic su **Salva**.  
     
        [![Aggiungere un gruppo di registrazione con il portale](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
        
        | Campo | Description |
        | :--- | :--- |
        | **Nome gruppo** | Nome obbligatorio per il gruppo di dispositivi. |
        | **Tipo di attestazione** | Fare clic su **certificato** o **chiave simmetrica** per il tipo di attestazione in base al metodo di attestazione che i dispositivi utilizzeranno. |
        | **Tipo di certificato** | Disponibile se si usa l'attestazione del certificato. Selezionare **certificato CA** o **intermedio** in base al certificato firmato per i certificati del dispositivo. |
        | **Certificato primario** | Se si firmano i certificati del dispositivo con un certificato CA radice, il certificato della CA radice deve avere una [prova di possesso](how-to-verify-certificates.md) completata. È quindi possibile selezionarlo come **certificato primario** per il gruppo di dispositivi.<br><br>Se si firmano i certificati del dispositivo con un certificato intermedio, sarà disponibile un pulsante Upload che consente di caricare il certificato intermedio. Il certificato che ha firmato il intermedio deve anche avere una [prova di possesso](how-to-verify-certificates.md) completata. |

        
    

* Una registrazione **singola** è una voce per un singolo dispositivo che può essere assegnato a un hub Internet. Sono supportati i dispositivi che usano la [chiave simmetrica](concepts-symmetric-key-attestation.md), i [certificati X. 509](concepts-x509-attestation.md)e l' [attestazione TPM](concepts-tpm-attestation.md) . 

    È possibile creare una registrazione singola nel portale mediante la procedura seguente:

    1. Accedere al portale di Azure e fare clic su **Tutte le risorse** nel menu a sinistra.
    1. Selezionare dall'elenco di risorse il servizio di provisioning dei dispositivi per cui si desidera registrare il dispositivo.
    1. Nel servizio di provisioning, fare clic su **Gestisci registrazioni**, quindi fare clic sul pulsante **Aggiungi registrazione singola** nella parte superiore.   

       [![Aggiungere una registrazione singola nel portale](./media/how-to-manage-enrollments/add-individual-enrollment.png)](./media/how-to-manage-enrollments/add-individual-enrollment.png#lightbox)

    1. Quando viene visualizzato il pannello "Aggiungi registrazione", immettere le informazioni per la registrazione del singolo dispositivo e fare clic su **Salva**. 
     
        [![Registrazione singola sul portale](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)
    
        | Campo | Description |
        | :--- | :--- |
        | **Meccanismo** | Selezionare **X. 509**, **TPM** o **chiave simmetrica** per il meccanismo di attestazione da utilizzare a seconda del metodo di attestazione che i dispositivi utilizzeranno. |
        | Impostazioni di attestazione | Per istruzioni dettagliate sulla creazione e l'uso di registrazioni singole con chiavi simmetriche o certificati X. 509, vedere una delle guide introduttive per il provisioning di un [dispositivo simmetrico](quick-create-simulated-device-symmetric-key-java.md#create-a-device-enrollment) o il [provisioning di un dispositivo di certificato x. 509](quick-create-simulated-device-x509-java.md#create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry) .<br><br>Per istruzioni dettagliate sulla creazione e l'uso di registrazioni singole con l'attestazione TPM, vedere uno degli esempi per il [provisioning di un dispositivo TPM simulato](quick-create-simulated-device-tpm-java.md#create-a-device-enrollment-entry) .|
        | **ID dispositivo hub Internet** |  Questo ID rappresenterà il dispositivo. Deve seguire le regole per un ID dispositivo. Per altre informazioni, vedere [proprietà delle identità dei dispositivi](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).<br><br>Quando si usano certificati X. 509, questo testo deve essere il nome del soggetto nel certificato del dispositivo caricato per la registrazione. Il nome del soggetto deve essere conforme alle regole per un ID dispositivo.|
            


## <a name="update-an-enrollment-entry"></a>Aggiornare una voce di registrazione
È possibile aggiornare una voce di registrazione esistente sul portale usando la procedura seguente:

1. Aprire il servizio di provisioning dei dispositivi nel portale di Azure e fare clic su **Manage enrollments** (Gestisci registrazioni). 
1. Passare alla voce di registrazione che si desidera modificare. Selezionando la voce, si aprirà un riepilogo dei dati per la registrazione del dispositivo. 
1. In questa pagina è possibile modificare elementi diversi dal tipo di sicurezza e dalle credenziali, ad esempio l'hub Internet delle cose a cui il dispositivo deve essere collegato e l'ID del dispositivo. È anche possibile modificare lo stato iniziale del dispositivo gemello. 
1. Al termine, fare clic su **Salva** per aggiornare la registrazione del dispositivo. 

    ![Aggiornare una registrazione sul portale](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Rimuovere una registrazione del dispositivo
Nei casi in cui non è necessario eseguire il provisioning dei dispositivi in alcun hub IoT, è possibile rimuovere la voce di registrazione correlata sul portale usando la procedura seguente:

1. Aprire il servizio di provisioning dei dispositivi nel portale di Azure e fare clic su **Manage enrollments** (Gestisci registrazioni). 
1. Individuare e selezionare la voce di registrazione che si desidera modificare. 
1. Fare clic su **Elimina** nella parte superiore, quindi selezionare **Sì** quando viene richiesto di confermare. 
1. Al termine dell'azione, la voce verrà rimossa dall'elenco delle registrazioni dei dispositivi. 
 
    ![Rimuovere una registrazione dal portale](./media/how-to-manage-enrollments/remove-enrollment.png)