---
title: includere il file
description: Includere file
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67180580"
---
## <a name="download-the-source-code"></a>Scaricare il codice sorgente

I repository di codice sorgente di monitoraggio remoto includono il codice sorgente e i file di configurazione Docker necessari per eseguire le immagini Docker dei microservizi.

Per clonare e creare una versione locale del repository, usare l'ambiente della riga di comando per passare a una cartella adatta nel computer locale. Eseguire quindi uno dei set di comandi seguenti per clonare il repository .NET:

Per scaricare la versione più recente delle implementazioni di microservizi .NET, eseguire:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Questi comandi scaricano il codice sorgente per tutti i microservizi oltre agli script usati per eseguire i microservizi in locale. Nonostante non sia necessario per eseguire i microservizi in Docker, il codice sorgente è utile se si intende modificare in un secondo momento l'acceleratore di soluzione e testare le modifiche in locale.

## <a name="deploy-the-azure-services"></a>Distribuire i servizi di Azure

Nonostante questo articolo illustri come eseguire i microservizi in locale, questi dipendono dai servizi di Azure eseguiti nel cloud. Usare lo script seguente per distribuire i servizi di Azure. Gli esempi di script seguenti presuppongono che si usi il repository .NET in un computer Windows. Se si lavora in un altro ambiente, modificare in modo appropriato i percorsi, le estensioni di file e i separatori di percorso.

### <a name="create-new-azure-resources"></a>Creare nuove risorse di Azure

Se non sono ancora state create le risorse di Azure necessarie, seguire questa procedura:

1. Nell'ambiente della riga di comando passare alla cartella **\services\scripts\local\launch** nella copia clonata del repository.

1. Eseguire i comandi seguenti per installare lo strumento dell'interfaccia della riga di comando **pcs** e accedere all'account di Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Eseguire lo script **start.cmd**. Lo script richiede le informazioni seguenti:
   * Nome della soluzione.
   * Sottoscrizione di Azure da usare.
   * Località del data center di Azure da usare.

     Lo script crea un gruppo di risorse in Azure con il nome della soluzione. Questo gruppo di risorse contiene le risorse di Azure usate dall'acceleratore di soluzione. È possibile eliminare il gruppo di risorse nel momento in cui le risorse corrispondenti non sono più necessarie.

     Lo script aggiunge anche un set di variabili di ambiente con un prefisso **PCS** nel computer locale. Queste variabili di ambiente forniscono i dettagli per il monitoraggio remoto per poter leggere da una risorsa Azure Key Vault. Questa risorsa Key Vault è il punto in cui il monitoraggio remoto leggerà i valori di configurazione da.

     > [!TIP]
     > Quando lo script viene completato, salva anche le variabili di ambiente in un file denominato ** \<cartella\>\\Home. PC\\\<\>soluzione nome. env**. Sarà possibile usarle per distribuzioni future di acceleratori di soluzione. Tutte le variabili di ambiente impostate nel computer locale ignorano i valori nel file **services\\scripts\\local\\.env** quando si esegue **docker-compose**.

1. Uscire dall'ambiente della riga di comando.

### <a name="use-existing-azure-resources"></a>Usare le risorse di Azure esistenti

Se sono già state create le risorse di Azure necessarie, creare le corrispondenti variabili di ambiente nel computer locale.
Impostare le variabili di ambiente per gli elementi seguenti:
* Nome **PCS_KEYVAULT_NAME** della risorsa Azure Key Vault
* **PCS_AAD_APPID** : ID dell'applicazione AAD
* **PCS_AAD_APPSECRET** : segreto dell'applicazione AAD

I valori di configurazione verranno letti da questa risorsa Azure Key Vault. Queste variabili di ambiente possono essere salvate nel file con ** \<estensione\>\\ENV della\\\<soluzione\>Home Folder. PCS** dalla distribuzione. Tutte le variabili di ambiente impostate nel computer locale ignorano i valori nel file **services\\scripts\\local\\.env** quando si esegue **docker-compose**.

Parte della configurazione richiesta dal microservizio è archiviata in un'istanza di **Key Vault** creata durante la distribuzione iniziale. Le variabili corrispondenti nell'insieme di credenziali delle credenziali devono essere modificate in base alle esigenze.