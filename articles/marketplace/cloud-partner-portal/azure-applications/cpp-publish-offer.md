---
title: Pubblicare l'offerta di un'applicazione Azure - Azure Marketplace | Microsoft Docs
description: Descrive il processo e la procedura per la pubblicazione di un'offerta per un'applicazione Azure in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pbutlerm
ms.openlocfilehash: 2adf07cf2337611b9136af47ce6a35b617e2e9ff
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55177033"
---
# <a name="publish-azure-application-offer"></a>Pubblicare un'offerta di applicazione Azure

Dopo avere creato un'offerta specificando le informazioni nella pagina **Nuova offerta**, è possibile pubblicarla. Selezionare **Pubblica** per avviare il processo di pubblicazione.

Il diagramma seguente illustra i passaggi principali del processo di pubblicazione che rende un'offerta disponibile in Azure Marketplace.

![Procedura di pubblicazione dell'offerta](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Descrizione dettagliata dei passaggi per la pubblicazione

La tabella seguente elenca e illustra ogni passaggio della pubblicazione e indica una stima dei tempi per completare ogni passaggio.  Le stime espresse in "giorni" si intendono in giorni lavorativi, che non comprendono i fine settimana e i giorni festivi.

|  **Passaggio per la pubblicazione**           | **Ora**    | **Descrizione**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Convalida dei prerequisiti         | < 15 min    | Vengono convalidate le informazioni e le impostazioni dell'offerta.                        |
| Convalida delle impostazioni dei ricavi influenzate | < 15 min  | Viene verificata l'attribuzione dell'utilizzo delle risorse di Azure per l'offerta.             |
| Certificazione                  | < 1 giorno     | L'offerta viene analizzata dal team delle certificazioni di Azure. Viene eseguita l'analisi dell'offerta per individuare eventuali virus, malware, conformità alle normative sulla sicurezza e problemi di sicurezza. L'offerta viene controllata per verificare che soddisfi tutti i criteri di idoneità. Per altre informazioni, vedere la sezione [Prerequisiti](./cpp-prerequisites.md). Se viene rilevato un problema, viene fornito un feedback. |
| Convalida del test drive          | < 2 ore   | (Facoltativo) Se è presente un test drive, Microsoft verifica che possa essere distribuito e replicato.  |
| Creazione di pacchetti e registrazione per la generazione di clienti potenziali | < 1 ora  | Le risorse tecniche dell'offerta vengono incluse in un pacchetto per uso dei clienti e i sistemi di registrazione di clienti potenziali vengono configurati e distribuiti. |
|  Approvazione del server di pubblicazione             |  manual    | Revisione e conferma finali dell'editore prima che l'offerta diventi disponibile in Azure Marketplace. L'offerta è ora disponibile in anteprima.  È possibile distribuire l'offerta nelle sottoscrizioni selezionate (nei passaggi relativi alle informazioni sull'offerta) per verificare che soddisfi tutti i requisiti.  Dopo aver verificato l'offerta, selezionare **Go Live** in modo che l'offerta passi alla fase successiva. |
| Revisione Microsoft                | 7 - 14 giorni | Microsoft esamina interamente l'applicazione Azure e, in caso di problemi, invia un messaggio di posta elettronica.  La durata di questo passaggio dipende dalla complessità dell'applicazione, dai problemi rilevati e dalla rapidità con cui si reagisce.  |
| Live                           | < 1 giorno | L'offerta viene rilasciata, replicata nelle aree specificate e resa disponibile al pubblico. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

 
È possibile monitorare il processo di pubblicazione nella scheda **Stato** per l'offerta nel portale Cloud Partner.

![Scheda Stato per l'offerta di un'app Azure](./media/offer-status-tab.png)

Al termine del processo di pubblicazione, l'offerta sarà disponibile nell'elenco della [categoria dell'applicazione di Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/).



## <a name="errors-and-review-feedback"></a>Errori e commenti e suggerimenti della revisione

Oltre a visualizzare lo stato della pubblicazione dell'offerta, la scheda **Stato** visualizza i messaggi di errore e i commenti e suggerimenti dal passaggio **Revisione Microsoft**.  I problemi di revisione vengono in genere chiamati richieste pull.  Ogni richiesta pull è collegata a un elemento di Visual Studio Team Services (VSTS, rinominato [Azure DevOps](https://azure.microsoft.com/services/devops/)) online, che contiene i dettagli del problema.  L'immagine seguente visualizza un esempio di riferimento a una richiesta pull di revisione.  In caso di situazioni più complesse, i team di revisione e supporto possono inviare un messaggio di posta elettronica. 

![Scheda Stato con i commenti e suggerimenti della revisione](./media/status-tab-ms-review.png)

È necessario risolvere ogni problema segnalato prima di proseguire con il processo di pubblicazione.  Il diagramma seguente illustra come questo processo di commenti e suggerimenti è correlato a quello di pubblicazione.

![Procedura di pubblicazione con i commenti e suggerimenti di VSTS](./media/pub-flow-vsts-access.png)


### <a name="vsts-access"></a>Accesso VSTS

Per visualizzare gli elementi VSTS a cui si fa riferimento nei commenti e suggerimenti della revisione, gli autori devono avere l'autorizzazione appropriata.  In caso contrario, i nuovi autori ricevono una pagina di risposta `401 - Not Authorized`.  Per richiedere l'accesso al sistema VSTS di revisione delle offerte, seguire questa procedura:

1. Raccogliere le informazioni seguenti:
    - Il nome dell'autore e l'ID
    - Tipo di offerta (app Azure), nome dell'offerta e ID SKU
    - Collegamento della richiesta pull, ad esempio: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` Questo URL può essere recuperato dal messaggio di notifica o dall'indirizzo della pagina di risposta 401.
    - Gli indirizzi di posta elettronica delle persone dell'organizzazione di pubblicazione a cui si vuole concedere l'accesso.  È necessario includere gli indirizzi dei proprietari specificati durante la registrazione come autore nel portale Cloud Partner.
2. Creare una richiesta di assistenza.  Nella barra del titolo del portale Cloud Partner selezionare il pulsante **?**, quindi scegliere **Supporto** dal menu.  Il Web browser predefinito verrà avviato e passerà alla pagina Microsoft della nuova richiesta di assistenza.  Potrebbe essere necessario eseguire prima l'accesso.
3. In **Tipo di problema** specificare **Onboarding del Marketplace** e in **Categoria** specificare **Problema di accesso**, quindi selezionare **Avvio richiesta**.

    ![Categoria del ticket di supporto](./media/support-incident1.png)

4. Nella pagina **Passaggio 1 di 2** specificare le informazioni sul contatto e selezionare **Continua**.
5. Nella pagina **Passaggio 2 di 2** specificare un titolo dell'evento imprevisto (ad esempio, `Request VSTS access`) e fornire le informazioni raccolte nel primo passaggio (sopra).  Leggere e accettare il contratto, quindi selezionare **Invia**.

Se la creazione dell'evento imprevisto ha esito positivo, viene visualizzata una pagina di conferma.  Salvare le informazioni di conferma per riferimento.  Il supporto tecnico Microsoft risponderà alla richiesta di accesso entro alcuni giorni lavorativi.


## <a name="next-steps"></a>Passaggi successivi

Dopo la pubblicazione di un'app Azure, è possibile [aggiornare l'offerta esistente](./cpp-update-existing-offer.md) per riflettere le modifiche ai requisiti tecnici o aziendali. 
