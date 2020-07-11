---
title: Configurare l'archivio protetto per Azure Data Box
description: Informazioni su come usare Customer Lockbox con Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 07/10/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 9c95760d03db976b59537adcecbe39a942b72126
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209567"
---
# <a name="use-customer-lockbox-for-azure-data-box-preview"></a>USA Customer Lockbox per Azure Data Box (anteprima)

Azure Data Box viene usato per trasferire i dati dei clienti da e verso Azure. Sono presenti istanze in cui supporto tecnico Microsoft potrebbe dover accedere ai dati del cliente durante una Richiesta di supporto. È possibile usare Customer Lockbox come interfaccia per esaminare e approvare o rifiutare queste richieste di accesso ai dati. 

Questo articolo illustra il modo in cui vengono avviate e registrate le richieste Customer Lockbox per Data Box importazione, nonché per gli ordini di esportazione. L'articolo si applica sia ai dispositivi Azure Data Box che ai dispositivi Azure Data Box Heavy. 

## <a name="devops-workflow-for-data-access"></a>Flusso di lavoro DevOps per l'accesso ai dati

Il team operativo di supporto e Data Box in Microsoft non accede in genere ai dati dei clienti. Tentano di risolvere i problemi usando gli strumenti standard e i dati di telemetria. <!--The only scenarios where there is a need to access customer data is when there is an issue with the data that needs to be fixed. For example, if the data is copied to a wrong folder or is in an incorrect format and is likely to result in an upload or download failure, then Microsoft will try to access your data in the Azure datacenter.--> 

Se i problemi non possono essere risolti e richiedono supporto tecnico Microsoft per analizzare o ripristinare i dati, richiedono l'accesso con privilegi elevati tramite il portale JIT (just-in-Time). Il portale di JIP convalida il livello di autorizzazione, fornisce l'autenticazione a più fattori e include anche un'approvazione dei responsabili approvazione interni di Microsoft. Ad esempio, il responsabile approvazione potrebbe essere DevOps Manager. 

Dopo che la richiesta di accesso con privilegi elevati è stata approvata tramite il portale JIT, se è stato abilitato l'archivio protetto, Microsoft richiederà anche il consenso esplicito per accedere ai dati. L'accesso viene richiesto e monitorato tramite il servizio Customer Lockbox nel portale. 

Se non è stato abilitato l'accesso protetto, il consenso non è necessario per accedere ai dati.


## <a name="prerequisites-for-access-request"></a>Prerequisiti per la richiesta di accesso

Prima di iniziare, verificare che:

1. È stato creato un ordine Azure Data Box in base alle istruzioni riportate in:
    1. [Esercitazione: ordinare Azure Data Box](data-box-deploy-ordered.md) per gli ordini di importazione.
    1. [Esercitazione: ordinare Azure Data Box](data-box-deploy-export-ordered.md) per gli ordini di esportazione.

2. Sono stati configurati Customer Lockbox per Data Box. Si tratta di un servizio di consenso esplicito. 

    1. Customer Lockbox è attualmente disponibile in anteprima per Data Box servizio. Per abilitare la Customer Lockbox per Data Box per l'organizzazione, iscriversi a [Customer Lockbox per l'anteprima pubblica di Azure](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Kwz02N6XVCoKNpxIpqE_hUNzlTUUNYVkozOVlFNVRSWDVHRkkwTFQyViQlQCN0PWcu).
    2. Customer Lockbox è automaticamente disponibile per tutti i clienti che dispongono di un piano di supporto di Azure con un livello minimo di sviluppatore. <!--How do you enable Lockbox? change this for Azure Data Box, perhaps you need a different support plan When you have an eligible support plan, no action is required by you to enable Customer Lockbox. Customer Lockbox requests are initiated by a Microsoft engineer if this action is needed to progress a support ticket that is filed from somebody in your organization.-->

3. Per questo problema è già stata aperta una richiesta di servizio o un ticket di supporto. Per informazioni sul ticket di supporto, vedere la pagina relativa alla [richiesta di un servizio per data box](data-box-disk-contact-microsoft-support.md).


## <a name="track-approve-request-via-lockbox"></a>Rileva, approva la richiesta tramite un archivio protetto

Per tenere traccia e approvare una richiesta di accesso ai dati dei clienti, attenersi alla procedura seguente:

1. Microsoft rileva che si è verificato un problema durante il caricamento o il download dei dati nel Data Center di Azure. Ad esempio, l'ordine di Data Box viene interrotto durante la fase di **copia dei dati** . 

    Il tecnico del supporto si connette a Data Box tramite la sessione di supporto e tenta di risolvere il problema usando gli strumenti standard e i dati di telemetria. Se i dischi Data Box sono bloccati e le condivisioni non sono accessibili, il tecnico del supporto crea una richiesta di archivio protetto. 
 
2. Quando viene creata la richiesta, in genere la notifica viene inviata all'amministratore della sottoscrizione, ma è anche possibile configurare un gruppo per le notifiche. 

3. Per l'approvazione, è possibile visualizzare la richiesta di archivio protetto nell'portale di Azure. 

    ![Richiesta in portale di Azure](./media/data-box-customer-lockbox/3-lockbox-request-azure-portal.png)

    Per approvare la richiesta di archivio protetto dal portale, è possibile selezionare **approva**.

    ![Approva richiesta](./media/data-box-customer-lockbox/4-lockbox-request-details-azure-portal.png)


    Una volta approvata la richiesta, i dischi del dispositivo vengono sbloccati e le condivisioni sono accessibili nella sessione di supporto.

4. Il tecnico del supporto risolve il problema di caricamento e quindi Disabilita la sessione di supporto.

Dopo che il problema è stato risolto, il processo di copia dei dati verrà completato.


## <a name="next-steps"></a>Passaggi successivi

- [Customer Lockbox per Microsoft Azure](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

<!--- [Approve, audit support access requests to VMs using Customer Lockbox for Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/)-->

