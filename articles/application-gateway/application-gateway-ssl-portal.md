---
title: Configurare l'offload SSL - Gateway applicazione di Azure - Portale di Azure | Documentazione Microsoft
description: Questo articolo contiene istruzioni per creare un gateway applicazione con offload SSL usando il portale di Azure
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 8373379a-a26a-45d2-aa62-dd282298eff3
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 23b5a529e7ee7db5615340352fb68b2e64e45972
ms.contentlocale: it-it
ms.lasthandoff: 08/30/2017

---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-azure-portal"></a>Configurare un gateway applicazione per l'offload SSL con il portale di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-ssl-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-ssl-arm.md)
> * [PowerShell classico per Azure](application-gateway-ssl.md)
> * [Interfaccia della riga di comando di Azure 2.0](application-gateway-ssl-cli.md)

Il gateway applicazione di Azure può essere configurato per terminare la sessione Secure Sockets Layer (SSL) nel gateway ed evitare costose attività di decrittografia SSL nella Web farm. L'offload SSL semplifica anche la configurazione e la gestione del server front-end dell'applicazione Web.

## <a name="scenario"></a>Scenario

Lo scenario seguente illustra la configurazione dell'offload SSL in un gateway applicazione esistente. Lo scenario presuppone che sia già stata seguita la procedura per [creare un gateway applicazione](application-gateway-create-gateway-portal.md).

## <a name="before-you-begin"></a>Prima di iniziare

Per configurare l'offload SSL con un gateway applicazione, è necessario un certificato, che viene caricato nel gateway applicazione e viene usato per crittografare e decrittografare il traffico inviato tramite SSL. Il certificato deve essere in formato PFX (Personal Information Exchange). Questo formato di file consente l'esportazione della chiave privata necessaria al gateway applicazione per eseguire la crittografia e la decrittografia del traffico.

## <a name="add-an-https-listener"></a>Aggiungere un listener HTTPS

Il listener HTTPS cerca il traffico in base alla relativa configurazione e consente di instradare il traffico ai pool back-end. Per aggiungere un listener HTTPS, seguire questi passaggi:

   1. Passare al portale di Azure e selezionare un gateway applicazione esistente.

   2. Selezionare **Listener** e quindi il pulsante **Aggiungi** per aggiungere un listener.

   ![Riquadro Panoramica del gateway applicazione][1]


   3. Inserire le informazioni necessarie seguenti per il listener e caricare il certificato con estensione pfx:
      - **Nome**: nome descrittivo del listener.

      - **Configurazione IP front-end**: la configurazione IP front-end usata per il listener.

      - **Porta front-end (Nome/Porta)**: nome descrittivo della porta usata nel front-end del gateway applicazione e porta effettiva usata.

      - **Protocollo**: opzione che consente di determinare se usare HTTPS o HTTP per il front-end.

      - **Certificato (Nome/Password)**: se si usa l'offload SSL, per questa impostazione è necessario un certificato con estensione pfx. Sono inoltre necessari un nome descrittivo e una password.

   4. Selezionare **OK**.

![Riquadro Aggiungi un listener][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>Creare una regola e associarla al listener

Ora che il listener è stato creato, È quindi necessario creare una regola per gestire il traffico dal listener. Le regole definiscono la modalità con cui il traffico viene indirizzato ai pool back-end in base a più impostazioni di configurazione. Queste impostazioni includono il protocollo, la porta e i probe di integrità e se l'affinità di sessione basata su cookie è in uso. Per creare e associare una regola al listener, eseguire la procedura seguente:


   1. Selezionare **Regole** del gateway applicazione e quindi **Aggiungi**.

   ![Riquadro Regole del gateway applicazione][3]


   2. In **Aggiungi regola di base** immettere un nome descrittivo della regola nel campo **Nome** e quindi scegliere il **Listener** creato nel passaggio precedente. Scegliere il **Pool back-end** e l'**impostazione HTTP** appropriati e quindi selezionare **OK**.

   ![Finestra delle impostazioni HTTPS][4]

Le impostazioni vengono così salvate nel gateway applicazione. Il processo di salvataggio di queste impostazioni può richiedere tempo e le impostazioni potrebbero non essere immediatamente visualizzabili tramite il portale o PowerShell. Dopo il salvataggio il gateway applicazione gestisce la crittografia e la decrittografia del traffico. Tutto il traffico tra il gateway applicazione e i server Web di back-end verrà gestito su HTTP. Tutte le comunicazioni verso il client, se avviate su HTTPS, verranno restituite al client crittografate.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare un probe di integrità personalizzato con un gateway applicazione di Azure, vedere [Creare un gateway applicazione con il portale](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png


