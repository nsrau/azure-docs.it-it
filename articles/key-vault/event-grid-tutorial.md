---
title: Ricevere e rispondere alle notifiche di Key Vault con Griglia di eventi di Azure
description: Informazioni su come integrare Key Vault con Griglia di eventi di Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 3b24da4d988554da240baba2984df44ff4744aaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464096"
---
# <a name="how-to-receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Procedura: Ricevere e rispondere alle notifiche di Key Vault con Griglia di eventi di Azure (anteprima)

L'integrazione di Key Vault con Griglia di eventi di Azure, attualmente in anteprima, consente agli utenti di ricevere notifiche quando lo stato di un segreto archiviato nell'insieme di credenziali delle chiavi è cambiato. Per una panoramica della funzionalità, vedere [Monitoraggio di Key Vault con Griglia di eventi di Azure](event-grid-overview.md).

Questa guida illustra come ricevere notifiche di Key Vault tramite Griglia di eventi di Azure e come rispondere alle modifiche dello stato con Automazione di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Un insieme di credenziali delle chiavi nella sottoscrizione di Azure. È possibile creare rapidamente un nuovo insieme di credenziali delle chiavi seguendo la procedura descritta in [Impostare e recuperare un segreto da Azure Key Vault usando l'interfaccia della riga di comando di Azure](quick-create-cli.md).

## <a name="concepts"></a>Concetti

La griglia di eventi di Azure è un servizio di gestione degli eventi per il cloud. In questa guida si sottoscriveranno eventi per l'insieme di credenziali delle chiavi e li si indirizzeranno ad Automazione di Azure. Quando uno dei segreti nell'insieme di credenziali delle chiavi sta per scadere, Griglia di eventi riceve una notifica della modifica dello stato e invia un POST HTTP all'endpoint. Un webhook attiva quindi un'esecuzione di Automazione di Azure dello script di PowerShell.

![image](media/image1.png)

## <a name="create-an-azure-automation-account"></a>Creare un account di Automazione di Azure

Creare un account di Automazione di Azure nel [portale di Azure](https://portal.azure.com).

1.  Passare a portal.azure.com e accedere alla sottoscrizione.

1.  Nella casella di ricerca digitare "Account di automazione".

1.  Nella sezione "Servizi" dell'elenco a discesa nella barra di ricerca selezionare "Account di Automazione".

1.  Fare clic su Aggiungi.

    ![](media/image2.png)

1.  Inserire le informazioni necessarie nel pannello "Aggiungi account di Automazione" e selezionare "Crea".

## <a name="create-a-runbook"></a>Creare un runbook

Quando l'account di Automazione di Azure è pronto, creare un runbook.

![](media/image3.png)

1.  Selezionare l'account di Automazione appena creato.

1.  Nella sezione Automazione processi selezionare "Runbook".

1.  Fare clic su "Crea un runbook".

1.  Assegnare un nome al runbook e selezionare "PowerShell" come tipo di runbook.

1.  Fare clic sul runbook creato e selezionare il pulsante "Modifica".

1.  Immettere il codice seguente (a scopo di test) e fare clic sul pulsante "Pubblica". Verrà restituito il risultato della richiesta POST ricevuta.

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![](media/image4.png)

## <a name="create-a-webhook"></a>Creare un webhook

A questo punto occorre creare un webhook per attivare il runbook appena creato.

1.  Selezionare "Webhook" nella sezione Risorse del runbook appena pubblicato.

1.  Fare clic su "Aggiungi webhook".

    ![](media/image5.png)

1.  Selezionare "Crea un nuovo webhook".

1. Assegnare un nome al webhook, impostare una data di scadenza e **copiare l'URL**.

    > [!IMPORTANT] 
    > Non è possibile visualizzare l'URL dopo averlo creato. Assicurarsi di salvarne una copia in una posizione sicura a cui sia possibile accedere per il resto di questa guida.

1. Fare clic su "Parametri e impostazioni di esecuzione" e scegliere "OK". Non immettere parametri. In questo modo verrà abilitato il pulsante "Crea".

1. Scegliere "OK" e selezionare "Crea".

    ![](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Creare una sottoscrizione di Griglia di eventi

Creare una sottoscrizione di Griglia di eventi tramite il [portale di Azure](https://portal.azure.com).

1.  Aprire il portale di Azure usando il collegamento seguente: https://portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true.

1.  Passare all'insieme di credenziali delle chiavi e selezionare la scheda "Eventi". Se la scheda Eventi non è visibile, assicurarsi di usare la [versione di anteprima del portale](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true).

    ![](media/image7.png)

1.  Fare clic sul pulsante "+ Sottoscrizione di eventi".

1.  Creare un nome descrittivo per la sottoscrizione.

1.  Scegliere "Schema griglia di eventi".

1.  "Risorsa argomento" deve essere l'insieme di credenziali delle chiavi di cui si vogliono monitorare le modifiche dello stato.

1.  Per "Filtra per tipi di evento" lasciare tutto selezionato ("9 selezionati").

1.  Per "Tipo di endpoint" selezionare "Webhook".

1.  Selezionare "Seleziona endpoint". Nel nuovo riquadro del contesto incollare l'URL del webhook dal passaggio [Creare un webhook](#create-a-webhook) nel campo "Endpoint sottoscrittore".

1.  Selezionare "Conferma selezione" nel riquadro del contesto.

1.  Selezionare "Crea".

    ![](media/image8.png)

## <a name="test-and-verify"></a>Test e verifica

Verificare che la sottoscrizione di Griglia di eventi sia configurata correttamente.  Questo test presuppone che sia stata sottoscritta la notifica di "creazione di una nuova versione di un segreto" in [Creare una sottoscrizione di Griglia di eventi](#create-an-event-grid-subscription) e che si abbiano i privilegi necessari per creare una nuova versione di un segreto in un insieme di credenziali delle chiavi.

![](media/image9.png)

![](media/image10.png)

1.  Passare all'insieme di credenziali delle chiavi nella portale di Azure

1.  Creare un nuovo segreto. Ai fini del test, impostare la data di scadenza sul giorno successivo.

1.  Passare alla scheda Eventi nell'insieme di credenziali delle chiavi.

1.  Selezionare la sottoscrizione di Griglia di eventi creata.

1.  In Metriche verificare se è stato acquisito un evento. Sono previsti due eventi: SecretNewVersion e SecretNearExpiry. Ciò conferma che la griglia di eventi ha acquisito correttamente la modifica dello stato del segreto nell'insieme di credenziali delle chiavi.

    ![](media/image11.png)

1.  Accedere all'account di Automazione di Azure.

1.  Selezionare la scheda "Runbook" e quindi il runbook creato.

1.  Selezionare la scheda "Webhook" e verificare che il timestamp "Ultima attivazione" sia entro 60 secondi dalla creazione del nuovo segreto.  Questo conferma che Griglia di eventi ha inviato un POST al webhook con i dettagli dell'evento relativi alla modifica dello stato nell'insieme di credenziali delle chiavi e che il webhook è stato attivato.

    ![](media/image12.png)

1. Tornare al runbook e selezionare la scheda "Panoramica".

1. Esaminare l'elenco Processi recenti. Si dovrebbe vedere che è stato creato un processo e che lo stato è Completato.  Questo conferma che il webhook ha attivato il runbook per avviare l'esecuzione dello script.

    ![](media/image13.png)

1. Selezionare il processo recente ed esaminare la richiesta POST inviata da Griglia di eventi al webhook. Esaminare il JSON e verificare che i parametri dell'insieme di credenziali delle chiavi e del tipo di evento siano corretti. Se il parametro del "tipo di evento" nell'oggetto JSON corrisponde all'evento che si è verificato nell'insieme di credenziali delle chiavi (in questo esempio Microsoft.KeyVault.SecretNearExpiry), il test è riuscito.

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="unable-to-create-event-subscription"></a>Non è possibile creare la sottoscrizione di eventi

Registrare di nuovo il provider dell'insieme di credenziali delle chiavi e Griglia di eventi nei provider di risorse della sottoscrizione di Azure. Vedere [Provider e tipi di risorse di Azure](../azure-resource-manager/resource-manager-supported-services.md).

## <a name="next-steps"></a>Passaggi successivi

Congratulazioni! Se sono stati eseguiti tutti i passaggi descritti in questa guida, è ora possibile rispondere a livello di codice alle modifiche dello stato dei segreti archiviati nell'insieme di credenziali delle chiavi.

Se si usa un sistema basato su polling per cercare le modifiche dello stato dei segreti negli insiemi di credenziali delle chiavi, eseguire la migrazione a questa funzionalità di notifica. È anche possibile sostituire lo script di test nel runbook con codice che imposti il rinnovo dei segreti a livello di programmazione quando stanno per scadere.

Altre informazioni:

- [Panoramica di Azure Key Vault](key-vault-overview.md)
- [Panoramica di Griglia di eventi di Azure](../event-grid/overview.md)
- [Monitoraggio di Key Vault con Griglia di eventi di Azure (anteprima)](event-grid-overview.md)
- [Schema di eventi di Griglia di eventi di Azure per Azure Key Vault (anteprima)](../event-grid/event-schema-key-vault.md)
- [Panoramica di Automazione di Azure](../automation/index.yml)
