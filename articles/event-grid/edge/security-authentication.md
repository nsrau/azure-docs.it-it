---
title: Sicurezza e autenticazione - Azure Event Grid IoT Edge Documenti Microsoft
description: Sicurezza e autenticazione in Griglia di eventi su Edge IoT.Security and authentication in Event Grid on IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5dfa17fd702b76e2cfaa7a91066dbc6749c1069e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844514"
---
# <a name="security-and-authentication"></a>Sicurezza e autenticazione

La sicurezza e l'autenticazione sono un concetto avanzato e richiedono prima la familiarità con le nozioni di base di Griglia di eventi. Inizia [da qui](concepts.md) se non hai familiarità con Griglia di eventi su IoT Edge. Il modulo Griglia di eventi si basa sull'infrastruttura di sicurezza esistente in IoT Edge. Fare riferimento a [questa documentazione](../../iot-edge/security.md) per i dettagli e la configurazione.

Nelle sezioni seguenti viene descritto in dettaglio come queste impostazioni vengono protette e autenticate:The following sections describe in detail how these settings are secured and authenticated:

* Configurazione TLS
* Autenticazione client in ingresso
* Autenticazione server in uscita
* Autenticazione client in uscita

>[!IMPORTANT]
>La sicurezza del modulo Griglia di eventi e la leva dell'autenticazione sono l'infrastruttura esistente disponibile su IoT Edge. Il presupposto è che il sottosistema IoT Edge sia sicuro.

>[!IMPORTANT]
>La configurazione della griglia di eventi è **sicura per impostazione predefinita.** Nelle sottosezioni seguenti vengono illustrate tutte le opzioni e i possibili valori che è possibile utilizzare per eseguire l'override degli aspetti dell'autenticazione. Comprendere l'impatto prima di apportare modifiche. Per rendere effettive le modifiche, sarà necessario ridistribuire il modulo Griglia di eventi.

## <a name="tls-configuration-aka-server-authentication"></a>Configurazione TLS (autenticazione del server)

Il modulo Griglia di eventi ospita gli endpoint HTTP e HTTPS. A ogni modulo IoT Edge viene assegnato un certificato server dal daemon di sicurezza di IoT Edge. Utilizziamo il certificato server per proteggere l'endpoint. Alla scadenza, il modulo viene aggiornato automaticamente con un nuovo certificato dal daemon di sicurezza IoT Edge.

Per impostazione predefinita, è consentita solo la comunicazione HTTPS. È possibile eseguire l'override di questo comportamento tramite la configurazione **di inbound__serverAuth__tlsPolicy.** Nella tabella seguente vengono acquisiti i possibili valori di questa proprietà.

| Valori possibili | Descrizione |
| ---------------- | ------------ |
| Strict | Valore predefinito. Abilita solo HTTPS
| Attivato | Abilita sia HTTP che HTTPS
| Disabled | Abilita solo HTTP

## <a name="inbound-client-authentication"></a>Autenticazione client in ingresso

I client sono entità che eseguono operazioni di gestione e/o di runtime. I client possono essere altri moduli IoT Edge, applicazioni non IoT.Clients can be other IoT Edge modules, non-IoT Applications.

Il modulo Griglia di eventi supporta due tipi di autenticazione client:

* Basato su chiave SAS (Shared Access Signature)
* basato su certificati

Per impostazione predefinita, il modulo Griglia di eventi è configurato per accettare solo l'autenticazione basata su certificati. All'avvio, il modulo Griglia di eventi recupera "TrustBundle" dal daemon di sicurezza IoT Edge e lo utilizza per convalidare qualsiasi certificato client. I certificati client che non vengono risolti `UnAuthorized`in questa catena verranno rifiutati con .

### <a name="certificate-based-client-authentication"></a>Autenticazione client basata su certificati

L'autenticazione basata su certificati è attivata per impostazione predefinita. È possibile scegliere di disabilitare l'autenticazione basata su certificati tramite la proprietà **inbound__clientAuth__clientCert__enabled**. Nella tabella seguente vengono acquisiti i possibili valori.

| Valori possibili | Descrizione |
| ----------------  | ------------ |
| true | Valore predefinito. Richiede che tutte le richieste nel modulo Griglia di eventi presentino un certificato client. Inoltre, sarà necessario configurare **inbound__clientAuth__clientCert__source**.
| false | Non forzare un client a presentare il certificato.

Nella tabella seguente vengono acquisiti i possibili valori per **inbound__clientAuth__clientCert__source**

| Valori possibili | Descrizione |
| ---------------- | ------------ |
| IoT Edge | Valore predefinito. Utilizza Trustbundle di IoT Edge per convalidare tutti i certificati client.

Se un client presenta un autofirmato, per impostazione predefinita, il modulo Griglia di eventi rifiuterà tali richieste. È possibile scegliere di consentire i certificati client autofirmati tramite **inbound__clientAuth__clientCert__allowUnknownCA** proprietà. Nella tabella seguente vengono acquisiti i possibili valori.

| Valori possibili | Descrizione |
| ----------------  | ------------|
| true | Valore predefinito. Consente la presentazione dei certificati autofirmati.
| false | Le richieste avrà esito negativo se vengono presentati certificati autofirmati.

>[!IMPORTANT]
>Negli scenari di produzione, è possibile impostare **inbound__clientAuth__clientCert__allowUnknownCA** **su false**.

### <a name="sas-key-based-client-authentication"></a>Autenticazione client basata su chiave di accesso client

Oltre all'autenticazione basata su certificati, il modulo Griglia di eventi può eseguire anche l'autenticazione basata sulla chiave di firma di accesso utente. La chiave di accesso condiviso è simile a un segreto configurato nel modulo Griglia di eventi che deve essere usato per convalidare tutte le chiamate in arrivo. I client devono specificare il segreto nell'intestazione HTTP 'aeg-sas-key'. La richiesta verrà `UnAuthorized` rifiutata se non corrisponde.

La configurazione per controllare l'autenticazione basata su chiave SAS è **inbound__clientAuth__sasKeys__enabled**.

| Valori possibili | Descrizione  |
| ----------------  | ------------ |
| true | Consente l'autenticazione basata su chiave di accesso utenti database. Richiede **inbound__clientAuth__sasKeys__key1** o **inbound__clientAuth__sasKeys__key2**
| false | Valore predefinito. L'autenticazione basata su chiave di chiamata di accesso utente è disabilitata.

 **inbound__clientAuth__sasKeys__key1** e **inbound__clientAuth__sasKeys__key2** sono chiavi che consentono di configurare il modulo Griglia di eventi per verificare rispetto alle richieste in arrivo. È necessario configurare almeno una delle chiavi. Il client che effettua la richiesta dovrà presentare la chiave come parte dell'intestazione della richiesta '**aeg-sas-key**'. Se entrambe le chiavi sono configurate, il client può presentare una delle chiavi.

> [!NOTE]
>È possibile configurare entrambi i metodi di autenticazione. In tal caso la chiave di firma di accesso utente viene controllata per prima e viene eseguita l'operazione solo in caso di errore, viene eseguita l'autenticazione basata su certificati. Affinché una richiesta abbia esito positivo, solo uno dei metodi di autenticazione deve avere esito positivo.

## <a name="outbound-client-authentication"></a>Autenticazione client in uscita

Il client nel contesto in uscita fa riferimento al modulo Griglia di eventi. L'operazione in corso è il recapito di eventi ai sottoscrittori. I moduli di sottoscrizione sono considerati come server.

A ogni modulo IoT Edge viene assegnato un certificato di identità dal daemon di sicurezza dell'Edge Edge. Usiamo il certificato di identità per le chiamate in uscita. Alla scadenza, il modulo viene aggiornato automaticamente con un nuovo certificato dal daemon di sicurezza IoT Edge.

La configurazione per controllare l'autenticazione client in uscita è **outbound__clientAuth__clientCert__enabled**.

| Valori possibili | Descrizione |
| ----------------  | ------------ |
| true | Valore predefinito. Richiede che tutte le richieste in uscita dal modulo Griglia di eventi presentino un certificato. È necessario configurare **outbound__clientAuth__clientCert__source**.
| false | Non richiedere al modulo Griglia di eventi di presentare il proprio certificato.

La configurazione che controlla l'origine del certificato viene **outbound__clientAuth__clientCert__source**.

| Valori possibili | Descrizione |
| ---------------- | ------------ |
| IoT Edge | Valore predefinito. Utilizza il certificato di identità del modulo configurato dal daemon di sicurezza di IoT Edge.

### <a name="outbound-server-authentication"></a>Autenticazione server in uscita

Uno dei tipi di destinazione per un sottoscrittore di Griglia di eventi è "Webhook". Per impostazione predefinita, solo gli endpoint HTTPS sono accettati per tali sottoscrittori.

Configurazione per controllare i criteri di destinazione del webhook **outbound__webhook__httpsOnly**.

| Valori possibili | Descrizione |
| ----------------  | ------------ |
| true | Valore predefinito. Consente solo i sottoscrittori con endpoint HTTPS.
| false | Consente ai sottoscrittori con endpoint HTTP o HTTPS.

Per impostazione predefinita, il modulo Griglia di eventi convaliderà il certificato server del sottoscrittore. È possibile ignorare la convalida eseguendo l'override di **outbound__webhook__skipServerCertValidation**. I valori possibili sono:

| Valori possibili | Descrizione |
| ----------------  | ------------ |
| true | Non convalidare il certificato server del sottoscrittore.
| false | Valore predefinito. Convalidare il certificato server del sottoscrittore.

Se il certificato del sottoscrittore è autofirmato, per impostazione predefinita il modulo Griglia di eventi rifiuterà tali sottoscrittori. Per consentire il certificato autofirmato, è possibile eseguire l'override **di outbound__webhook__allowUnknownCA**. Nella tabella seguente vengono acquisiti i possibili valori.

| Valori possibili | Descrizione |
| ----------------  | ------------ |
| true | Valore predefinito. Consente la presentazione dei certificati autofirmati.
| false | Le richieste avrà esito negativo se vengono presentati certificati autofirmati.

>[!IMPORTANT]
>Negli scenari di produzione è consigliabile impostare **outbound__webhook__allowUnknownCA** **su false**.

> [!NOTE]
>L'ambiente IoT Edge genera certificati autofirmati. Si consiglia di generare certificati emessi da CA autorizzate per i carichi di lavoro di produzione e di impostare la proprietà **allowUnknownCA** sia in ingresso che in uscita su **false**.

## <a name="summary"></a>Riepilogo

Un modulo Griglia di eventi è **protetto per impostazione predefinita.** È consigliabile mantenere queste impostazioni predefinite per le distribuzioni di produzione.

Di seguito sono riportati i principi guida da utilizzare durante la configurazione:

* Consentire solo le richieste HTTPS nel modulo.
* Consentire solo l'autenticazione client basata su certificati. Consentire solo i certificati emessi da CA note. Non consentire certificati autofirmati.
* Non consentire l'autenticazione client basata su SASKey.
* Presentare sempre il certificato di identità del modulo Griglia di eventi nelle chiamate in uscita.
* Consentire solo i sottoscrittori HTTPS per i tipi di destinazione Webhook.
* Convalidare sempre il certificato server del sottoscrittore per i tipi di destinazione Webhook. Consentire solo i certificati emessi da CA note. Non consentire certificati autofirmati.

Per impostazione predefinita, il modulo Griglia di eventi viene distribuito con la configurazione seguente:By default, Event Grid module is deployed with the following configuration:

 ```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```
