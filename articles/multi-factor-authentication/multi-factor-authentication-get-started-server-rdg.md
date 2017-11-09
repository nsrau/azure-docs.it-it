---
title: Gateway Desktop remoto e server Azure MFA con RADIUS | Documentazione Microsoft
description: "Questa è la pagina di Azure Multi-factor authentication che sarà utile per la distribuzione di Gateway Desktop remoto (RD) e Server Azure Multi-Factor Authentication tramite RADIUS."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: f2354ac4-a3a7-48e5-a86d-84a9e5682b42
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/27/2017
ms.author: joflore
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: 653712aebd274349b030eae653a8ea670e438d93
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>Gateway Desktop remoto e server Azure Multi-Factor Authentication utilizzando RADIUS
Gateway Desktop remoto usa il server dei criteri di rete locale per autenticare gli utenti. Questo articolo descrive come inoltrare richieste RADIUS da Gateway Desktop remoto (tramite il server dei criteri di rete locale) al server Multi-Factor Authentication. La combinazione di Azure MFA e Gateway Desktop remoto consente agli utenti di accedere agli ambienti di lavoro da qualsiasi luogo eseguendo l'autenticazione avanzata. 

Dato che l'autenticazione di Windows per Servizi terminal non è supportata per Server 2012 R2, usare RADIUS e Gateway Desktop remoto per l'integrazione con il server MFA. 

Installare il server Multi-Factor Authentication in un server separato, che trasmetterà tramite proxy la richiesta RADIUS al server dei criteri di rete nel server Gateway Desktop remoto. Dopo aver convalidato il nome utente e la password, il server dei criteri di rete restituisce una risposta al server Multi-Factor Authentication. Il server MFA esegue quindi il secondo fattore dell'autenticazione e restituisce un risultato al gateway.

## <a name="prerequisites"></a>Prerequisiti

- Un server MFA di Azure aggiunto a un dominio. Se il server non è già installato, seguire i passaggi in [Introduzione al server Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server.md).
- Un'istanza di Gateway Desktop remoto che esegue l'autenticazione con il server dei criteri di rete.

## <a name="configure-the-remote-desktop-gateway"></a>Configurare Gateway Desktop remoto
Configurare Gateway Desktop remoto per l'invio dell'autenticazione RADIUS a un server Azure Multi-Factor Authentication. 

1. In Gestione Gateway Desktop remoto fare clic con il pulsante destro del mouse sul nome server e scegliere **Proprietà**.
2. Passare alla scheda **Archivio criteri di autorizzazione connessioni Desktop remoto** e selezionare **Server dei criteri di rete centrale**. 
3. Aggiungere uno o più server Azure Multi-Factor Authentication come server RADIUS immettendo il nome o l'indirizzo IP di ogni server. 
4. Creare un segreto condiviso per ogni server.

## <a name="configure-nps"></a>Configurazione dei criteri di rete
Il Gateway Desktop remoto utilizza criteri di rete per inviare richieste RADIUS a Azure Multi-Factor Authentication. Per configurare il server dei criteri di rete, modificare prima le impostazioni di timeout per evitare il timeout di Gateway Desktop remoto prima che venga completata la verifica in due passaggi. Aggiornare quindi il server dei criteri di rete per la ricezione delle autenticazioni RADIUS dal server MFA. Usare la procedura seguente per configurare il server dei criteri di rete:

### <a name="modify-the-timeout-policy"></a>Modificare i criteri di timeout

1. In Server dei criteri di rete espandere il menu **Client e server RADIUS** nella colonna a sinistra e selezionare **Gruppi di server RADIUS remoti**. 
2. Selezionare il gruppo **SERVER GATEWAY DI SERVIZI TERMINAL**. 
3. Passare alla scheda **Bilanciamento del carico**. 
4. Impostare sia **Numero di secondi senza risposta prima che la richiesta venga annullata** che **Numero di secondi tra le richieste quando il server viene identificato come non disponibile** su un valore compreso tra 30 e 60 secondi. Se il timeout del server persiste durante l'autenticazione, è possibile tornare a questa schermata e aumentare il numero di secondi.
5. Passare alla scheda **Autenticazione/Account** e verificare che le porte RADIUS specificate corrispondano alle porte di attesa del server Multi-Factor Authentication.

### <a name="prepare-nps-to-receive-authentications-from-the-mfa-server"></a>Preparare il server dei criteri di rete per la ricezione delle autenticazioni dal server MFA

1. Fare clic con il pulsante destro del mouse su **Client RADIUS** in Client e server RADIUS nella colonna a sinistra e scegliere **Nuovo**.
2. Aggiungere il Server Azure Multi-Factor Authentication come client RADIUS. Scegliere un nome descrittivo e specificare un segreto condiviso.
3. Aprire il menu **Criteri** nella colonna a sinistra e selezionare **Criteri di richiesta di connessione**. Verrà visualizzato un criterio denominato TS GATEWAY AUTHORIZATION POLICY, creato durante la configurazione di Gateway Desktop remoto. Questo criterio inoltra le richieste RADIUS al Server Multi-Factor Authentication.
4. Fare clic con il pulsante destro del mouse su **TS GATEWAY AUTHORIZATION POLICY** e scegliere **Duplica criterio**. 
5. Aprire il nuovo criterio e selezionare la scheda **Condizioni**.
6. Aggiungere una condizione che associ il nome descrittivo del client al nome descrittivo impostato nel passaggio 2 per il client RADIUS del server Azure Multi-Factor Authentication. 
7. Passare alla scheda **Impostazioni** e selezionare **Autenticazione**.
8. Modificare il provider di autenticazione in **Autentica le richieste su questo server**. Questo criterio fa sì che quando il server dei criteri di rete riceve una richiesta RADIUS dal server MFA, l'autenticazione viene eseguita localmente, invece di inviare una richiesta RADIUS al server Azure Multi-Factor Authentication creando una condizione di ciclo. 
9. Per evitare una condizione di ciclo, assicurarsi che il nuovo criterio PRECEDA il criterio originale nel riquadro **Criteri di richiesta di connessione**.

## <a name="configure-azure-multi-factor-authentication"></a>Configurazione di Azure Multi-Factor Authentication

Il Server Azure Multi-Factor Authentication è configurato come proxy RADIUS tra Gateway Desktop remoto e criteri di rete.  È necessario installarlo in un server di dominio che è separato dal server Gateway Desktop remoto. Utilizzare la procedura seguente per configurare il Server Azure Multi-Factor Authentication.

1. Aprire il server Azure Multi-Factor Authentication e fare clic sull'icona Autenticazione RADIUS. 
2. Selezionare la casella di controllo **Abilita autenticazione RADIUS**.
3. Nella scheda Client assicurarsi che le porte corrispondano a quelle configurate in Server dei criteri di rete, quindi fare clic su **Aggiungi**.
4. Aggiungere l'indirizzo IP del server Gateway Desktop remoto, il nome dell'applicazione (facoltativo) e un segreto condiviso. Il segreto condiviso deve essere lo stesso per Gateway Desktop remoto e il server Azure Multi-Factor Authentication.
3. Passare alla scheda **Destinazione** e selezionare il pulsante di opzione **Server RADIUS**.
4. Selezionare **Aggiungi** e immettere l'indirizzo IP, il segreto condiviso e le porte del server dei criteri di rete. Se non si usa un server dei criteri di rete centrale, il client RADIUS e la destinazione RADIUS saranno uguali. Il segreto condiviso deve corrispondere a quello impostato nella sezione client RADIUS del server dei criteri di rete.

![Autenticazione RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="next-steps"></a>Passaggi successivi

- Integrare Azure MFA e [app Web IIS](multi-factor-authentication-get-started-server-iis.md)

- Vedere le [Domande frequenti su Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)
