---
title: Ripristino da un errore a livello di area
description: Informazioni su come app Azure Service consente di mantenere le funzionalità di continuità aziendale e ripristino di emergenza (BCDR). Ripristinare l'app da un errore a livello di area in Azure.
ms.topic: how-to
ms.date: 06/09/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 1388dc11254324f74efcbaa55c97cac2ccd0c026
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073737"
---
# <a name="move-an-app-service-app-to-another-region"></a>Spostare un'app del servizio app in un'altra area

Questo articolo descrive come riportare online le risorse del servizio app in un'area di Azure diversa durante un'emergenza che influisca su un'intera area di Azure. Quando un'emergenza porta offline un'intera area di Azure, tutte le app del servizio app ospitate in tale area vengono inserite in modalità di ripristino di emergenza. Sono disponibili funzionalità che consentono di ripristinare l'app in un'area diversa o di ripristinare i file dall'app interessata.

Le risorse del servizio app sono specifiche dell'area e non possono essere spostate tra le aree. È necessario ripristinare l'app in una nuova app in un'area diversa, quindi creare configurazioni di mirroring o risorse per la nuova app.

## <a name="prerequisites"></a>Prerequisiti

- No. Il [ripristino dallo snapshot](app-service-web-restore-snapshots.md) richiede in genere il livello **Premium** , ma in modalità di ripristino di emergenza viene abilitato automaticamente per l'app interessata, indipendentemente dal livello in cui si trova l'app interessata.

## <a name="prepare"></a>Preparazione

Identificare tutte le risorse del servizio app attualmente utilizzate dall'app interessata. Ad esempio:

- App del servizio app
- [Piani del servizio app](overview-hosting-plans.md)
- [Slot di distribuzione](deploy-staging-slots.md)
- [Domini personalizzati acquistati in Azure](manage-custom-dns-buy-domain.md)
- [Certificati SSL](configure-ssl-certificate.md)
- [Integrazione della rete virtuale di Azure](web-sites-integrate-with-vnet.md)
- [Connessioni ibride](app-service-hybrid-connections.md).
- [Identità gestite](overview-managed-identity.md)
- [Impostazioni di backup](manage-backup.md)

Alcune risorse, ad esempio i certificati importati o le connessioni ibride, contengono l'integrazione con altri servizi di Azure. Per informazioni su come spostare tali risorse tra le aree, vedere la documentazione relativa ai rispettivi servizi.

## <a name="restore-app-to-a-different-region"></a>Ripristinare un'app in un'area diversa

1. Creare una nuova app del servizio app in un'area di Azure *diversa* dall'app interessata. Si tratta dell'app di destinazione nello scenario di ripristino di emergenza.

1. Nella [portale di Azure](https://portal.azure.com)passare alla pagina di gestione dell'app interessata. In un'area di Azure non riuscita, l'app interessata Mostra un testo di avviso. Fare clic sul testo dell'avviso.

    ![Screenshot della pagina dell'app interessata. Viene visualizzata una notifica di avviso che descrive la situazione e fornisce un collegamento per il ripristino dell'app.](media/manage-disaster-recovery/restore-start.png)

1. Nella pagina **Ripristina backup** configurare l'operazione di ripristino in base alla tabella seguente. Al termine, fare clic su **OK**.

   | Impostazione | Valore | Descrizione |
   |-|-|-|
   | **Snapshot (anteprima)** | Selezionare uno snapshot. | Sono disponibili i due snapshot più recenti. |
   | **Ripristina destinazione** | **App esistente** | Fare clic sulla nota seguente **per fare clic qui per modificare l'app di destinazione Restore** e selezionare l'app di destinazione. In uno scenario di emergenza, è possibile ripristinare lo snapshot solo in un'app in un'area di Azure diversa. |
   | **Ripristinare la configurazione del sito** | **Sì** | |

    ![Screenshot della pagina di ripristino del backup. Uno snapshot specifico, le opzioni elencate nella tabella precedente e il pulsante OK sono evidenziati.](media/manage-disaster-recovery/restore-configure.png)

3. Configurare [tutti gli altri elementi](#prepare) nell'app di destinazione per eseguire il mirroring dell'app interessata e verificare la configurazione.

4. Quando si è pronti per il dominio personalizzato in modo che punti all'app di destinazione, [modificare il mapping del nome di dominio](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

## <a name="recover-app-content-only"></a>Ripristina solo il contenuto dell'app

Se si vuole solo ripristinare i file dall'app interessata senza ripristinarli, seguire questa procedura:

1. Nella [portale di Azure](https://portal.azure.com)passare alla pagina di gestione dell'app interessata e fare clic su **Ottieni profilo di pubblicazione**.

    ![Screenshot della pagina dell'app interessata. Una notifica di avviso è visibile ma non è evidenziata. L'elemento Get Publish profile è invece evidenziato.](media/manage-disaster-recovery/get-publish-profile.png)

1. Aprire il file scaricato e trovare il profilo di pubblicazione che contiene `ReadOnly - FTP` nel nome. Si tratta del profilo di ripristino di emergenza. Ad esempio:

    ```xml
    <publishProfile profileName="%app-name% - ReadOnly - FTP" publishMethod="FTP" publishUrl="ftp://%ftp-site%/site/wwwroot" ftpPassiveMode="True" userName="%app-name%\$%app-name%" userPWD="" destinationAppUrl="http://%app-name%.azurewebsites.net" SQLServerDBConnectionString="" mySQLDBConnectionString="" hostingProviderForumLink="" controlPanelLink="http://windows.azure.com" webSystem="WebSites">
        <databases />
    </publishProfile>
    ```
    
    Copiare tre valori di attributo: 
        
    - `publishUrl`: nome host FTP
    - `userName` e `userPWD` : credenziali FTP

1. Usare il client FTP preferito, connettersi all'host FTP dell'app interessata usando il nome host e le credenziali.

1. Una volta stabilita la connessione, scaricare l'intera cartella */site/wwwroot* . Lo screenshot seguente illustra come scaricare in [FileZilla](https://filezilla-project.org/).

    ![Screenshot della gerarchia dei file FileZilla. La cartella wwwroot è evidenziata e il menu di scelta rapida è visibile. In quel menu viene evidenziato il download.](media/manage-disaster-recovery/download-content.png)

## <a name="next-steps"></a>Passaggi successivi
[Ripristinare un'app in Azure da uno snapshot](app-service-web-restore-snapshots.md)
