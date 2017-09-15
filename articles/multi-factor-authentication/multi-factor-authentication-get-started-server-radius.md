---
title: Autenticazione RADIUS e server Azure MFA | Microsoft Docs
description: "Questa è la pagina di Azure Multi-Factor Authentication contenente le informazioni utili per distribuire l'autenticazione RADIUS e il server Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: f4ba0fb2-2be9-477e-9bea-04c7340c8bce
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/25/2017
ms.author: joflore
ms.reviewer: 
ms.custom: H1Hack27Feb2017, it-pro
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 4dfa56ba6f80193e643965b97b6439c62f7873e0
ms.contentlocale: it-it
ms.lasthandoff: 08/29/2017

---
# <a name="integrate-radius-authentication-with-azure-multi-factor-authentication-server"></a>Integrare l'autenticazione con il server Azure Multi-Factor Authentication

RADIUS è un protocollo standard per accettare le richieste di autenticazione e per elaborare le richieste. Il server Azure Multi-Factor Authentication può fungere da server RADIUS. Per aggiungere la verifica in due passaggi, inserirlo tra il client RADIUS (appliance VPN) e la destinazione di autenticazione, che potrebbe essere Active Directory, una directory LDAP o un altro server RADIUS. Per far funzionare Azure Multi-Factor Authentication (MFA), è necessario configurare il server Azure MFA in modo che possa comunicare con i server client e la destinazione di autenticazione. Il server Azure MFA accetta richieste da un client RADIUS, convalida le credenziali rispetto alla destinazione di autenticazione, aggiunge Azure Multi-Factor Authentication e invia una risposta al client RADIUS. La richiesta di autenticazione avrà esito positivo solo se l'autenticazione principale e Azure Multi-Factor Authentication hanno esito positivo.

> [!NOTE]
> Il server MFA supporta solo i protocolli RADIUS PAP (Password Authentication Protocol) e MSCHAPv2 (Microsoft Challenge Handshake Authentication Protocol) quando agisce da server RADIUS.  Quando il server MFA agisce come proxy RADIUS per un altro server RADIUS che supporta tale protocollo, è possibile usare altri protocolli, ad esempio EAP (Extensible Authentication Protocol).
>
> In questa configurazione i token OATH e SMS unidirezionali non funzionano perché il server MFA non è in grado di avviare una risposta RADIUS in attesa corretta tramite protocolli alternativi.

![Autenticazione RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="add-a-radius-client"></a>Aggiungere un client RADIUS
Per configurare l'autenticazione RADIUS, installare il Server Azure Multi-Factor Authentication su un server Windows. Se si dispone di un ambiente Active Directory, il server deve appartenere al dominio all'interno della rete. Utilizzare la procedura seguente per configurare il Server Azure Multi-Factor Authentication:

1. Nel server Azure Multi-Factor Authentication fare clic sull'icona autenticazione RADIUS nel menu a sinistra.
2. Selezionare la casella di controllo **Abilita autenticazione RADIUS**.
3. Nella scheda Client modificare le porte di autenticazione e accounting se il servizio RADIUS di Azure MFA deve attendere le richieste RADIUS su porte non standard.
4. Fare clic su **Aggiungi**.
5. Immettere l'indirizzo IP dell'accessorio/server che eseguirà l'autenticazione al server Azure Multi-Factor Authentication, il nome di un'applicazione (facoltativo) e un segreto condiviso.

  Il nome dell'applicazione viene visualizzato nei report ed eventualmente nei messaggi di autenticazione tramite app per dispositivi mobili o SMS.

  Il segreto condiviso dovrà essere lo stesso per il server Azure Multi-Factor Authentication e l'accessorio/server.

6. Se tutti gli utenti sono stati importati nel server e sono soggetti all'autenticazione a più fattori, selezionare la casella **Richiedi corrispondenza utente di Multi-Factor Authentication**. Se un numero significativo di utenti non è ancora stato importato nel server o non è soggetto alla verifica in due passaggi, lasciare deselezionata la casella.
7. Se si vogliono usare passcode OATH delle app di verifica per dispositivi mobili come metodo di backup, selezionare la casella **Abilita token OATH di fallback**.
8. Fare clic su **OK**.

Ripetere i passaggi da 4 a 8 per aggiungere tutti i client RADIUS che si desidera.

## <a name="configure-your-radius-client"></a>Configurare il client RADIUS

1. Fare clic sulla scheda **Destinazione**.
2. Se il server Azure MFA è installato in un server aggiunto a un dominio in un ambiente Active Directory, selezionare il dominio di Windows.
3. Se gli utenti devono essere autenticati in una directory LDAP, selezionare **Binding LDAP**.

  Selezionare l'icona Integrazione directory e modificare la configurazione LDAP nella scheda Impostazioni per consentire l'associazione del server alla directory. Le istruzioni per la configurazione LDAP sono disponibili nella [Guida alla configurazione del proxy LDAP](multi-factor-authentication-get-started-server-ldap.md).

4. Se gli utenti devono essere autenticati in un altro server RADIUS, selezionare il/i server RADIUS.
5. Fare clic su **Aggiungi** per configurare il server in cui il server Azure MFA userà il proxy per le richieste RADIUS.
6. Nella finestra di dialogo Aggiungi server RADIUS immettere l'indirizzo IP del server RADIUS e un segreto condiviso.

  Il segreto condiviso deve essere lo stesso per il server RADIUS e per il server Azure Multi-Factor Authentication. Se il server RADIUS utilizza porte diverse, modificare le porte di autenticazione e Accounting.

7. Fare clic su **OK**.
8. Aggiungere il server Azure MFA come client RADIUS negli altri server RADIUS in modo che questo elabori le richieste di accesso ricevute dal server Azure MFA. Usare lo stesso segreto condiviso configurato nel server Azure Multi-Factor Authentication.

Ripetere questi passaggi per aggiungere altri server RADIUS. Configurare l'ordine in cui devono essere chiamati dal server Azure MFA con i pulsanti **Sposta su** e **Sposta giù**.

La configurazione del server Azure Multi-Factor Authentication è stata completata. Il Server è in attesa sulle porte configurate per le richieste di accesso RADIUS dai client configurati.   

## <a name="radius-client-configuration"></a>Configurazione del client RADIUS
Per configurare il client RADIUS, utilizzare le linee guida:

* Configurare l'appliance/server per l'autenticazione tramite RADIUS all'indirizzo IP del server Azure Multi-Factor Authentication, che funge da server RADIUS.
* Usare lo stesso segreto condiviso configurato in precedenza.
* Impostare il timeout di RADIUS su un valore compreso tra 30 e 60 secondi in modo da rendere disponibile una quantità di tempo sufficiente per convalidare le credenziali dell'utente, eseguire l'autenticazione in due passaggi, ricevere la risposta e quindi rispondere alla richiesta di accesso RADIUS.

## <a name="next-steps"></a>Passaggi successivi

Se si usa Azure Multi-Factor Authentication nel cloud, vedere l'articolo relativo all'[integrazione con l'autenticazione RADIUS](multi-factor-authentication-nps-extension.md). 
