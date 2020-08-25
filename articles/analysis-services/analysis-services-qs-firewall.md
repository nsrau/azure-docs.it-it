---
title: Guida di avvio rapido - Configurare il firewall del server di Azure Analysis Services | Microsoft Docs
description: Questa Guida di avvio rapido consente di configurare un firewall per un server di Azure Analysis Services usando il portale di Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e4953137cf939c35c6ac73fe51ca43eca6e99edc
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192430"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Guida di avvio rapido: Configurare il firewall del server - Portale

Questa guida di avvio rapido consente di configurare un firewall per il server di Azure Analysis Services. L'abilitazione di un firewall e la configurazione di intervalli di indirizzi IP solo per i computer che accedono al server sono una parte importante della protezione dei dati e del server.

## <a name="prerequisites"></a>Prerequisiti

- Un server di Analysis Services nella sottoscrizione. Per altre informazioni, vedere [Guida di avvio rapido: Creare un server - Portale](analysis-services-create-server.md) o [Guida di avvio rapido: Creare un server - PowerShell](analysis-services-create-powershell.md)
- Uno o più intervalli di indirizzi IP per i computer client (se necessario).

> [!NOTE]
> Le connessioni per l'importazione di dati (aggiornamento) e i report impaginati da Power BI Premium in Microsoft Cloud Germania non sono attualmente supportate quando è abilitato un firewall, anche quando l'impostazione Consenti l'accesso da Power BI è attivata.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure 

[Accedere al portale](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Configurare un firewall

1. Fare clic sul server per aprire la pagina Panoramica. 
2. In **IMPOSTAZIONI** > **Firewall** > **Abilita firewall** selezionare **Sì**.
3. Per abilitare le connessioni da Power BI e Power BI Premium, in **Consenti l'accesso da Power BI** selezionare **Sì**.  
4. (Facoltativo) Specificare uno o più intervalli di indirizzi IP. Immettere un nome e un indirizzo IP iniziale e finale per ogni intervallo. Il nome della regola firewall deve essere limitato a 128 caratteri e può contenere solo caratteri maiuscoli, caratteri minuscoli, numeri, underscore e trattini. Non sono consentiti spazi vuoti e altri caratteri speciali.
5. Fare clic su **Salva**.

     ![Impostazioni del firewall](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare gli intervalli di indirizzi IP o disabilitare il firewall.

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva è stato illustrato come configurare un firewall per il server. Dopo avere creato il server e averlo protetto con un firewall, è possibile aggiungervi un modello di dati di esempio di base dal portale. Un modello di esempio è utile per avere informazioni sulla configurazione dei ruoli del database modello e sul test delle connessioni client. Per altre informazioni, continuare con l'esercitazione per l'aggiunta di un modello di esempio.

> [!div class="nextstepaction"]
> [Esercitazione: Aggiungere un modello di esempio al server](analysis-services-create-sample-model.md)
