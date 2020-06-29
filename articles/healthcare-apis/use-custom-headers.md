---
title: Aggiungere dati ai log di controllo usando intestazioni personalizzate - API di Azure per FHIR
description: Questo articolo descrive come aggiungere dati ai log di controllo usando intestazioni HTTP personalizzate nell'API di Azure per FHIR.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: d274160cc2ed1102dfc8fd11df358b34e40d9923
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "84870300"
---
# <a name="add-data-to-audit-logs-by-using-custom-http-headers"></a>Aggiungere dati ai log di controllo usando intestazioni HTTP personalizzate

Nell'API di Azure per FHIR (Fast Healthcare Interoperability Resources) un utente potrebbe voler includere informazioni aggiuntive nei log, che provengono dal sistema chiamante.

Ad esempio, quando l'utente dell'API viene autenticato da un sistema esterno, quest'ultimo inoltra la chiamata all'API per FHIR. Al livello dell'API per FHIR le informazioni sull'utente originale vanno perse perché la chiamata è stata inoltrata. Potrebbe essere necessario registrare e conservare queste informazioni dell'utente a scopo di controllo o di gestione. Nelle intestazioni HTTP il sistema chiamante può fornire l'identità dell'utente, la posizione del chiamante o altre informazioni necessarie, che verranno trasferite quando la chiamata viene inoltrata.

È possibile visualizzare questo flusso di dati nel diagramma seguente:

:::image type="content" source="media/custom-headers/custom-headers-diagram.png" alt-text="Diagramma delle intestazioni personalizzate":::

È possibile usare le intestazioni personalizzate per acquisire diversi tipi di informazioni. Ad esempio:

* Informazioni su identità o autorizzazioni
* Origine del chiamante
* Organizzazione di origine
* Dettagli del sistema client (cartella clinica elettronica, portale del paziente)

> [!IMPORTANT]
> Tenere presente che le informazioni inviate nelle intestazioni personalizzate vengono archiviate in un sistema di registrazione interno Microsoft per 30 giorni dopo essere state rese disponibili in Monitoraggio log di Azure. È consigliabile crittografare tutte le informazioni prima di aggiungerle alle intestazioni personalizzate. Non è consigliabile passare informazioni personali tramite intestazioni personalizzate.

Per le intestazioni HTTP, è necessario usare la convenzione di denominazione seguente: X-MS-AZUREFHIR-AUDIT-AUDIT-\<name>.

Queste intestazioni HTTP sono incluse in un elenco di proprietà aggiunto al log. Ad esempio:

* X-MS-AZUREFHIR-AUDIT-USERID: 1234 
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: XXXX
* X-MS-AZUREFHIR-AUDIT-XYZ: 1234

Queste informazioni vengono quindi serializzate in JSON quando vengono aggiunte alla colonna di proprietà nel log. Ad esempio:

```json
{ "X-MS-AZUREFHIR-AUDIT-USERID" : "1234",
"X-MS-AZUREFHIR-AUDIT-USERLOCATION" : "XXXX",
"X-MS-AZUREFHIR-AUDIT-XYZ" : "1234" }
```
 
Come per qualsiasi intestazione HTTP, lo stesso nome di intestazione può essere ripetuto con valori diversi. Ad esempio:

* X-MS-AZUREFHIR-AUDIT-USERLOCATION: HospitalA
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: Emergenza

Quando vengono aggiunti al log, i valori vengono combinati con un elenco delimitato da virgole. Ad esempio:

{ "X-MS-AZUREFHIR-AUDIT-USERLOCATION" : "HospitalA, Emergency" }
 
È possibile aggiungere un massimo di 10 intestazioni univoche (le ripetizioni della stessa intestazione con valori diversi vengono conteggiate come una). La lunghezza massima totale del valore per qualsiasi intestazione è 2048 caratteri.

Se si usa la libreria di API client C# Firely, il codice avrà un aspetto simile al seguente:

```C#
FhirClient client;
client = new FhirClient(serverUrl);
client.OnBeforeRequest += (object sender, BeforeRequestEventArgs e) =>
{
    // Add custom headers to be added to the logs
    e.RawRequest.Headers.Add("X-MS-AZUREFHIR-AUDIT-UserLocation", "HospitalA");
};
client.Get("Patient");
```
## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come aggiungere dati ai log di controllo usando intestazioni personalizzate nell'API di Azure per FHIR. Leggere ora informazioni sulle altre impostazioni che è possibile configurare nell'API di Azure per FHIR.
 
>[!div class="nextstepaction"]
>[Impostazioni aggiuntive](azure-api-for-fhir-additional-settings.md)