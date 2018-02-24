---
title: Informazioni sulla creazione di una definizione dell'interfaccia utente per le applicazioni gestite di Azure | Microsoft Docs
description: Illustra come creare definizioni dell'interfaccia utente per le applicazioni gestite di Azure
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: 1e995a3de33960fa3255074a704528bec9d21491
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>Creare l'interfaccia utente del portale di Azure per l'applicazione gestita
Questo documento illustra i concetti di base del file createUiDefinition.json. Il portale di Azure usa questo file per generare l'interfaccia utente per la creazione di un'applicazione gestita.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

CreateUiDefinition contiene sempre tre proprietà: 

* handler
* version
* Parametri

Per le applicazioni gestite la proprietà handler deve essere sempre `Microsoft.Compute.MultiVm` e la versione supportata più recente è `0.1.2-preview`.

Lo schema della proprietà parameters dipende dalla combinazione delle proprietà handler e version specificate. Per le applicazioni gestite sono supportate le proprietà `basics`, `steps` e `outputs`. Le proprietà basics e steps contengono _elementi_, ad esempio caselle di testo ed elenchi a discesa, da visualizzare nel portale di Azure. La proprietà outputs viene usata per il mapping dei valori di output degli elementi specificati ai parametri del modello di distribuzione Azure Resource Manager.

L'inclusione di `$schema` è consigliata ma facoltativa. Se specificato, il valore per la proprietà `version` deve corrispondere alla versione nell'URI di `$schema`.

## <a name="basics"></a>Nozioni di base
Il passaggio relativo alle informazioni di base è sempre il primo passaggio della procedura guidata generata quando il portale di Azure analizza il file. Oltre a visualizzare gli elementi specificati in `basics`, il portale inserisce elementi che consentono agli utenti di scegliere la sottoscrizione, il gruppo di risorse e la posizione della distribuzione. In genere gli elementi che eseguono query per parametri a livello di distribuzione, ad esempio il nome di un cluster o le credenziali dell'amministratore, devono essere inseriti in questo passaggio.

Se il comportamento di un elemento dipende dalla sottoscrizione, dal gruppo di risorse o dalla posizione dell'utente, non è possibile usare tale elemento per le proprietà basics. Ad esempio, **Microsoft.Compute.SizeSelector** dipende dalla sottoscrizione e dalla posizione dell'utente per la determinazione dell'elenco delle dimensioni disponibili. È quindi possibile usare **Microsoft.Compute.SizeSelector** solo nelle proprietà steps. In genere è possibile usare nelle proprietà basics solo gli elementi disponibili nello spazio dei nomi **Microsoft.Common**. Sono tuttavia consentiti alcuni elementi disponibili in altri spazi dei nomi, ad esempio **Microsoft.Compute.Credentials**, che non dipendono dal contesto dell'utente.

## <a name="steps"></a>Passaggi
La proprietà steps può contenere zero o più passaggi aggiuntivi da visualizzare dopo la proprietà basics, ognuna delle quali contiene uno o più elementi. Prendere in considerazione l'aggiunta di passaggi per ogni ruolo o livello dell'applicazione in fase di distribuzione. Aggiungere ad esempio un passaggio per gli input dei nodi master e un passaggio per i nodi di lavoro in un cluster.

## <a name="outputs"></a>Output
Il portale di Azure usa la proprietà `outputs` per il mapping di elementi da `basics` e `steps` ai parametri del modello di distribuzione Azure Resource Manager. Le chiavi di questo dizionario sono i nomi dei parametri del modello e i valori sono le proprietà degli oggetti di output dagli elementi a cui si fa riferimento.

Per impostare il nome della risorsa applicazione gestita, è necessario includere un valore denominato `applicationResourceName` nella proprietà di output. Se non si imposta questo valore, l'applicazione assegna un GUID per il nome. È possibile includere una casella di testo nell'interfaccia utente che richiede un nome all'utente.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="functions"></a>Funzioni
Analogamente alle funzioni del modello in Azure Resource Manager, a livello di sintassi e di funzionalità, CreateUiDefinition offre funzioni per l'uso di input e output degli elementi, oltre a funzionalità quali le istruzioni condizionali.

## <a name="next-steps"></a>Passaggi successivi
Il file createUiDefinition.json è contraddistinto da uno schema semplice ma supporta numerosi elementi e funzioni, illustrati in modo dettagliato nei documenti seguenti:

- [Elementi](create-uidefinition-elements.md)
- [Funzioni](create-uidefinition-functions.md)

Per uno schema JSON corrente per createUiDefinition, vedere qui: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

Per un esempio di file di interfaccia utente, vedere [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json).
