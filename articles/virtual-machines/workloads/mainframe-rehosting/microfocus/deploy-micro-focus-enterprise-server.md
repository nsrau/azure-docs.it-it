---
title: Distribuire Micro Focus Enterprise Server 5,0 a AKS | Microsoft Docs
description: Riospitare i carichi di lavoro mainframe IBM z/OS usando l'ambiente di sviluppo e test con Micro Focus in macchine virtuali (VM) di Azure.
services: virtual-machines-linux
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 6780942d922f885c7afebd8e64f4f28654c3800e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87042549"
---
# <a name="deploy-micro-focus-enterprise-server-50-to-aks"></a>Distribuire Micro Focus Enterprise Server 5,0 ad AKS

In un altro [articolo](./run-enterprise-server-container.md)sono stati illustrati i passaggi per eseguire Micro Focus Enterprise Server 5,0 in un contenitore docker. Per completare questa procedura, desidero illustrare come eseguire un ulteriore passaggio e distribuire l'immagine Docker creata nel servizio Azure Kubernetes (AKS).

Il servizio Azure Kubernetes è un servizio di orchestrazione gestito basato su Kubernetes. Consente di distribuire, ridimensionare e gestire contenitori Docker (e altre applicazioni basate su contenitori) in un cluster di host contenitore.

Si tratta di un processo in tre fasi. È necessario:

1.  Creare una Container Registry di Azure per archiviare l'immagine docker.

2.  Creare un cluster Azure Kubernetes per eseguire l'immagine docker.

3.  Eseguire l'applicazione.

In questo modo è possibile aumentare e ridurre i carichi di lavoro di modernizzazione del mainframe in Azure, sfruttando il vero vantaggio della piattaforma cloud.

Pronto? Iniziamo!

## <a name="create-the-azure-container-registry"></a>Creare il Container Registry di Azure

Dalla portale di Azure selezionare **Crea una risorsa** nell'angolo in alto a sinistra. Dal dashboard del Marketplace selezionare **contenitori,** quindi **container Registry**. In questo modo si passa al riquadro **Crea registro contenitori** in cui è necessario compilare il **nome del registro di sistema**, la **sottoscrizione di Azure**, il **gruppo di risorse**e la **località**. Il **nome del registro di sistema** deve essere risolto, quindi deve essere univoco. Selezionare il **gruppo di risorse** usato dal post di Blog precedente e lo stesso **percorso**corrispondente. Selezionare **Abilita** per **utente amministratore** e di **base** per lo **SKU**. Una volta completate tutte le operazioni, selezionare **Crea**.

![Crea interfaccia del registro contenitori](media/deploy-image-1.png)

Dopo aver distribuito il registro di sistema, selezionare **Vai alla risorsa**. Viene quindi installato il pannello principale per la Container Registry di Azure. Una funzionalità interessante è la **avvio rapido** opzione di menu. Selezionarlo per visualizzare le istruzioni su come eseguire il push e il pull delle immagini da e verso il registro di sistema. Si procederà però come segue:

1.  **Installare Docker** : non è necessario preoccuparsi di questo perché è già stato fatto.

2.  **Eseguire l'immagine di base "Hello-World"** . anche in questo caso non è necessario, ma è possibile provarlo.

3.  **Accedere al registro contenitori** : è necessario eseguire questa operazione dalla macchina virtuale (VM). Copiare il comando negli Appunti o nel blocco note.

    Per il registro di sistema creato, il comando è: **Docker login acrmf50.azurecr.io**

4.  **Push al registro di** sistema: è necessario eseguire questa operazione anche per l'immagine di Micro Focus dopo aver eseguito l'accesso alla macchina virtuale.

5.  Effettuare **il pull dal registro** di sistema: non è pertinente per questa procedura dettagliata, ma è opportuno conoscere se è necessario eseguire un'altra immagine docker.

Prima di uscire dal portale, è necessario ottenere le credenziali per il registro di sistema, in modo da poter eseguire l'accesso. Uscire dal pannello **avvio rapido** e selezionare chiavi di **accesso** dal menu Registro di sistema. Copiare il **nome utente** e una delle **password** (due) negli Appunti o nel blocco note. Sarà necessario in seguito per l'accesso.

Ora che si è in grado di eseguire le operazioni necessarie, accedere alla macchina virtuale.

## <a name="rdp-to-the-virtual-machine-you-used-to-create-the-docker-image"></a>RDP per la macchina virtuale usata per creare l'immagine Docker

Poiché è già stata creata l'immagine Docker in un server Windows 2016, è necessario accedere a tale macchina virtuale. Da questa macchina virtuale è possibile eseguire il push dell'immagine nel Container Registry di Azure appena creato. Passare alla macchina virtuale nel portale di Azure, quindi selezionare **Panoramica** e quindi **Connetti**. In questo modo si connette alla macchina virtuale tramite Remote Desktop Protocol (RDP). È necessario usare le credenziali da quando è stata creata la macchina virtuale.

## <a name="log-in-and-push-the-image-to-the-registry"></a>Accedere ed eseguire il push dell'immagine nel registro di sistema

Dopo aver eseguito l'accesso, aprire un prompt dei comandi e avviare i comandi di Docker seguenti:

-   **Immagini Docker** : Mostra un elenco di tutte le immagini attualmente installate nella macchina virtuale. Prendere nota della **micromessa a fuoco/es-acctdemo,** perché si tratta di quello che verrà usato.

-   **Docker login acrmf50.azurecr.io** : il formato corretto qui è *Docker login \<registry name\> *. Sostituire il nome usato durante la creazione del registro di sistema.

    -   Sono necessari il **nome utente** e la **password** copiati dal portale di Azure. Il risultato dovrebbe essere simile all'immagine seguente.

    ![Screenshot del prompt dei comandi dell'amministratore](media/deploy-image-2.png)

-   **Docker Tag microfocus/es-acctdemo acrmf50.azurecr.io/es-acctdemo** : questo tag è l'immagine appropriata con il nome del repository. **Nota**: se il nome non \<microfocus/es-acctdemo\> funziona, provare a usare l'ID immagine completo. Dopo aver eseguito il comando, digitare **Docker images-no-tronca**. Verrà visualizzata una schermata simile all'immagine successiva. Si noti che l'immagine è contrassegnata correttamente.

    ![Schermata di selezione del prompt dei comandi dell'amministratore](media/deploy-image-3.png)

-   **Docker push acrmf50.azurecr.io/es-acctdemo** : consente di avviare il push effettivo nel repository. Poiché la dimensione è 15 GB, l'esecuzione richiede un paio di minuti. Se tutto va bene, viene visualizzata una schermata simile all'immagine seguente.

    ![Schermata del prompt dei comandi dell'amministratore](media/deploy-image-4.png)

Tornare ora alla portale di Azure, in modo specifico per il **repository**. Nel menu per il **repository** **selezionare repository e verrà**visualizzato **es-acctdemo** . A questo punto, creare il cluster AKS.

## <a name="create-the-azure-kubernetes-aks-cluster"></a>Creare il cluster Azure Kubernetes (AKS)

Dal portale di Azure selezionare **Crea una risorsa** e quindi **contenitori/servizio Kubernetes** dal menu **Marketplace** . Successivamente, è necessario compilare il pannello **Crea cluster Kubernetes** . Assicurarsi di tenere il cluster nella stessa area e nel gruppo di risorse in uso. È possibile accettare le altre impostazioni predefinite eccetto il **numero di nodi,** che deve essere solo 1. Al termine, selezionare **Rivedi e crea**.

![Schermata di creazione del cluster Kubernetes](media/deploy-image-5.png)

Al termine della distribuzione, gli elementi del **servizio Kubernetes** vengono posizionati nel **gruppo di risorse** selezionato nel pannello. Tuttavia, il cluster effettivo avrà un proprio gruppo di risorse creato durante la distribuzione. Se si seleziona **gruppi di risorse** dal menu a sinistra, è possibile trovarlo in base alla convenzione di denominazione. Ecco un'immagine del mio, ovvero l'ultimo nell'elenco.

![Screenshot dei gruppi di risorse](media/deploy-image-6.png)

**Eseguire l'immagine**

A questo punto è possibile eseguire il pull dell'immagine ed eseguirla in AKS. Il modo più semplice per eseguire questa operazione dal portale di Azure consiste nell'usare il Cloud Shell. Si troverà l'icona in alto a destra del portale di Azure. Si noti che per questa procedura dettagliata si usa la shell bash.

![Screenshot dell'icona Cloud Shell](media/deploy-image-7.png)

Una volta caricata la shell, digitare il comando seguente:

**kubectl eseguire es-acctdemo--image acrmf50.azurecr.io/es-acctdemo--Port = 9040**

Questa operazione estrae l'immagine dal repository **acrmf50.azurecr.io** e la carica in AKS. Esegue quindi l'immagine con la porta 9040 aperta. È possibile ricordare che si tratta della porta aperta per l'immagine docker. È necessario per accedere a Enterprise Server.

Kubernetes deve rispondere con un messaggio in cui è stata creata la distribuzione.

![Screenshot di un messaggio di distribuzione](media/deploy-image-8.jpg)

Per verificare se il contenitore è effettivamente in esecuzione, digitare: **kubectl Get pods**.

Verrà visualizzato es-acctdemo come pod in esecuzione, come illustrato nella figura seguente.

![Screenshot es-acctdemo come pod in esecuzione](media/deploy-image-9.png)

A questo punto, Si sta ora eseguendo Enterprise Server nel servizio Azure Kubernetes. Nel prossimo articolo verrà illustrato come accedere alla console di amministrazione di Enterprise Server e come sfruttare i Kubernetes per scalare la distribuzione.
