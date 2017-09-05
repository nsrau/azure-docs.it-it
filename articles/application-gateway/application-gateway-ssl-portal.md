---
title: Configurare l'offload SSL - Gateway applicazione di Azure - Portale di Azure | Documentazione Microsoft
description: Questa pagina contiene istruzioni per creare un gateway applicazione con offload SSL usando il portale
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 8373379a-a26a-45d2-aa62-dd282298eff3
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: f61be0cc4c9274c9914f7c468ce48a2a3d0a4f4a
ms.contentlocale: it-it
ms.lasthandoff: 08/04/2017

---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-portal"></a>Configurare un gateway applicazione per l'offload SSL con il portale

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-ssl-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-ssl-arm.md)
> * [PowerShell per Azure classico](application-gateway-ssl.md)
> * [Interfaccia della riga di comando di Azure 2.0](application-gateway-ssl-cli.md)

Il gateway applicazione di Azure può essere configurato per terminare la sessione Secure Sockets Layer (SSL) nel gateway ed evitare costose attività di decrittografia SSL nella Web farm. L'offload SSL semplifica anche la configurazione e la gestione del server front-end dell'applicazione Web.

## <a name="scenario"></a>Scenario

Lo scenario seguente illustra la configurazione dell'offload SSL in un gateway applicazione esistente. Lo scenario presuppone che sia già stata seguita la procedura per [creare un gateway applicazione](application-gateway-create-gateway-portal.md).

## <a name="before-you-begin"></a>Prima di iniziare

Per configurare l'offload SSL con un gateway applicazione, è necessario un certificato, che viene caricato nel gateway applicazione e viene usato per crittografare e decrittografare il traffico inviato tramite SSL. Il certificato deve essere in formato PFX (Personal Information Exchange). Questo formato di file consente l'esportazione della chiave privata necessaria al gateway applicazione per eseguire la crittografia e la decrittografia del traffico.

## <a name="add-an-https-listener"></a>Aggiungere un listener HTTPS

Il listener HTTPS cerca il traffico in base alla relativa configurazione e consente di instradare il traffico ai pool back-end.

### <a name="step-1"></a>Passaggio 1

Passare al portale di Azure e selezionare un gateway applicazione esistente.

### <a name="step-2"></a>Passaggio 2

Fare clic su Listener e quindi sul pulsante Aggiungi per aggiungere un listener.

![Pannello di panoramica del gateway applicazione][1]

### <a name="step-3"></a>Passaggio 3

Inserire le informazioni necessarie per il listener e caricare il certificato PFX. Al termine, fare clic su OK.

**Nome** : questo valore è un nome descrittivo del listener.

**Configurazione IP front-end** : questo valore è la configurazione IP front-end usata per il listener.

**Porta front-end (Nome/Porta)** : nome descrittivo della porta usata nel front-end del gateway applicazione e porta effettiva usata.

**Protocollo** : opzione che consente di determinare se usare HTTPS o HTTP per il front-end.

**Certificato (Nome/Password)** : se si usa l'offload SSL, per questa impostazione sono necessari un certificato PFX, un nome descrittivo e una password.

![Pannello Aggiungi listener][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>Creare una regola e associarla al listener

Ora che il listener è stato creato, è necessario creare una regola per gestire il traffico dal listener. Le regole definiscono in che modo il traffico viene indirizzato ai pool back-end sulla base di varie impostazioni di configurazione, tra cui se viene usata l'affinità di sessione basata su cookie, nonché il protocollo, la porta e i probe di integrità.

### <a name="step-1"></a>Passaggio 1

Fare clic su **Regole** nel gateway applicazione e quindi su Aggiungi.

![Pannello delle regole del gateway applicazione][3]

### <a name="step-2"></a>Passaggio 2

Nel pannello **Aggiungi regola di base** digitare il nome descrittivo della regola e scegliere il listener creato nel passaggio precedente. Scegliere il pool back-end e l'impostazione HTTP appropriati e fare clic su **OK**

![Finestra delle impostazioni HTTP][4]

Le impostazioni vengono così salvate nel gateway applicazione. Il processo di salvataggio di queste impostazioni può richiedere tempo e le impostazioni potrebbero non essere immediatamente visualizzabili tramite il portale o PowerShell. Al termine del salvataggio, il gateway applicazione gestisce la crittografia e la decrittografia del traffico. Tutto il traffico tra il gateway applicazione e i server Web back-end verrà gestito su HTTP. Tutte le comunicazioni verso il client, se avviate su HTTPS, verranno restituite al client crittografate.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare un probe di integrità personalizzato con un gateway applicazione di Azure, vedere [Creare un gateway applicazione con il portale](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

