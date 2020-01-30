---
title: Sicurezza e autenticazione-IoT Edge di griglia di eventi di Azure | Microsoft Docs
description: Sicurezza e autenticazione in griglia di eventi in IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5dfa17fd702b76e2cfaa7a91066dbc6749c1069e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844514"
---
# <a name="security-and-authentication"></a>Sicurezza e autenticazione

Sicurezza e autenticazione è un concetto avanzato che richiede prima di tutto una certa familiarità con le nozioni di base di griglia di eventi. Iniziare da [qui](concepts.md) se non si ha familiarità con griglia di eventi in IOT Edge. Il modulo di griglia di eventi si basa sull'infrastruttura di sicurezza esistente in IoT Edge. Per informazioni dettagliate e configurazione, fare riferimento a [questa documentazione](../../iot-edge/security.md) .

Le sezioni seguenti descrivono in dettaglio il modo in cui queste impostazioni sono protette e autenticate:

* Configurazione TLS
* Autenticazione client in ingresso
* Autenticazione server in uscita
* Autenticazione client in uscita

>[!IMPORTANT]
>La sicurezza e l'autenticazione del modulo griglia di eventi sfruttano l'infrastruttura esistente disponibile in IoT Edge. Il presupposto è che IoT Edge sottosistema sia protetto.

>[!IMPORTANT]
>La configurazione di griglia **di eventi è protetta per impostazione predefinita**. Le sottosezioni seguenti illustrano tutte le opzioni e i possibili valori che è possibile usare per eseguire l'override degli aspetti dell'autenticazione. Comprendere l'effetto prima di apportare modifiche. Per rendere effettive le modifiche, sarà necessario ridistribuire il modulo di griglia di eventi.

## <a name="tls-configuration-aka-server-authentication"></a>Configurazione TLS (a. k. autenticazione server)

Il modulo di griglia di eventi ospita sia endpoint HTTP che HTTPS. A ogni modulo IoT Edge viene assegnato un certificato server dal daemon di sicurezza IoT Edge. Il certificato del server viene usato per proteggere l'endpoint. Alla scadenza, il modulo viene aggiornato automaticamente con un nuovo certificato dal daemon di sicurezza IoT Edge.

Per impostazione predefinita, sono consentite solo le comunicazioni HTTPS. È possibile eseguire l'override di questo comportamento tramite **inbound__serverAuth__tlsPolicy** configurazione. Nella tabella seguente vengono acquisiti i valori possibili di questa proprietà.

| Valori possibili | Description |
| ---------------- | ------------ |
| Restrittivo | Valore predefinito. Abilita solo HTTPS
| Attivato | Abilita sia HTTP che HTTPS
| Disabled | Abilita solo HTTP

## <a name="inbound-client-authentication"></a>Autenticazione client in ingresso

I client sono entità che eseguono operazioni di gestione e/o di Runtime. I client possono essere costituiti da altri moduli IoT Edge e da applicazioni diverse.

Il modulo di griglia di eventi supporta due tipi di autenticazione client:

* Firma di accesso condiviso (SAS) basata su chiavi
* Basato su certificati

Per impostazione predefinita, il modulo di griglia di eventi è configurato per accettare solo l'autenticazione basata sui certificati. All'avvio, il modulo di griglia di eventi recupera "TrustBundle" da IoT Edge daemon di sicurezza e lo usa per convalidare qualsiasi certificato client. I certificati client che non vengono risolti in questa catena verranno rifiutati con `UnAuthorized`.

### <a name="certificate-based-client-authentication"></a>Autenticazione client basata su certificati

L'autenticazione basata su certificati è attiva per impostazione predefinita. È possibile scegliere di disabilitare l'autenticazione basata su certificati tramite la proprietà **inbound__clientAuth__clientCert__enabled**. Nella tabella seguente vengono acquisiti i valori possibili.

| Valori possibili | Description |
| ----------------  | ------------ |
| true | Valore predefinito. Richiede che tutte le richieste nel modulo di griglia di eventi presentino un certificato client. Inoltre, sarà necessario configurare **inbound__clientAuth__clientCert__source**.
| false | Non forzare un client a presentare il certificato.

Nella tabella seguente vengono acquisiti i valori possibili per **inbound__clientAuth__clientCert__source**

| Valori possibili | Description |
| ---------------- | ------------ |
| IoT Edge | Valore predefinito. Usa il Trustbundle di IoT Edge per convalidare tutti i certificati client.

Se un client presenta un autofirmato, per impostazione predefinita il modulo di griglia di eventi rifiuterà tali richieste. È possibile scegliere di consentire i certificati client autofirmati tramite **inbound__clientAuth__clientCert__allowUnknownCA** proprietà. Nella tabella seguente vengono acquisiti i valori possibili.

| Valori possibili | Description |
| ----------------  | ------------|
| true | Valore predefinito. Consente di presentare correttamente i certificati autofirmati.
| false | Se vengono presentati certificati autofirmati, le richieste avranno esito negativo.

>[!IMPORTANT]
>Negli scenari di produzione può essere opportuno impostare **inbound__clientAuth__clientCert__allowUnknownCA** su **false**.

### <a name="sas-key-based-client-authentication"></a>Autenticazione client basata su chiave SAS

Oltre all'autenticazione basata su certificati, il modulo di griglia di eventi può anche eseguire l'autenticazione basata su chiave SAS. La chiave SAS è come un segreto configurato nel modulo di griglia di eventi che deve usare per convalidare tutte le chiamate in ingresso. I client devono specificare il segreto nell'intestazione HTTP "AEG-SAS-Key". La richiesta verrà rifiutata con `UnAuthorized` se non corrisponde.

La configurazione per controllare l'autenticazione basata su chiave SAS è **inbound__clientAuth__sasKeys__enabled**.

| Valori possibili | Description  |
| ----------------  | ------------ |
| true | Consente l'autenticazione basata su chiave SAS. Richiede **inbound__clientAuth__sasKeys__key1** o **inbound__clientAuth__sasKeys__key2**
| false | Valore predefinito. L'autenticazione basata su chiave SAS è disabilitata.

 **inbound__clientAuth__sasKeys__key1** e **inbound__clientAuth__sasKeys__key2** sono chiavi che consentono di configurare il modulo di griglia di eventi per verificare la presenza di richieste in ingresso. Almeno una delle chiavi deve essere configurata. Il client che effettua la richiesta dovrà presentare la chiave come parte dell'intestazione della richiesta "**AEG-SAS-Key**". Se entrambe le chiavi sono configurate, il client può presentare uno dei due tasti.

> [!NOTE]
>È possibile configurare entrambi i metodi di autenticazione. In tal caso, la chiave di firma di accesso condiviso viene controllata per prima e solo se l'operazione non riesce, viene eseguita l'autenticazione basata sui certificati. Affinché una richiesta abbia esito positivo, solo uno dei metodi di autenticazione deve avere esito positivo.

## <a name="outbound-client-authentication"></a>Autenticazione client in uscita

Il client nel contesto in uscita fa riferimento al modulo di griglia di eventi. L'operazione eseguita sta distribuendo gli eventi ai sottoscrittori. I moduli di sottoscrizione sono considerati server.

A ogni modulo IoT Edge viene assegnato un certificato di identità dal daemon di sicurezza della IoT Edge. Viene usato il certificato di identità per le chiamate in uscita. Alla scadenza, il modulo viene aggiornato automaticamente con un nuovo certificato dal daemon di sicurezza IoT Edge.

La configurazione per controllare l'autenticazione client in uscita è **outbound__clientAuth__clientCert__enabled**.

| Valori possibili | Description |
| ----------------  | ------------ |
| true | Valore predefinito. Richiede che tutte le richieste in uscita dal modulo griglia di eventi presentino un certificato. Deve configurare **outbound__clientAuth__clientCert__source**.
| false | Non richiedere il modulo di griglia di eventi per presentare il certificato.

La configurazione che controlla l'origine del certificato è **outbound__clientAuth__clientCert__source**.

| Valori possibili | Description |
| ---------------- | ------------ |
| IoT Edge | Valore predefinito. Usa il certificato di identità del modulo configurato da IoT Edge daemon di sicurezza.

### <a name="outbound-server-authentication"></a>Autenticazione server in uscita

Uno dei tipi di destinazione di un Sottoscrittore di griglia di eventi è "webhook". Per impostazione predefinita, vengono accettati solo endpoint HTTPS per tali Sottoscrittori.

La configurazione per controllare i criteri di destinazione del webhook **outbound__webhook__httpsOnly**.

| Valori possibili | Description |
| ----------------  | ------------ |
| true | Valore predefinito. Consente solo i sottoscrittori con endpoint HTTPS.
| false | Consente ai sottoscrittori con endpoint HTTP o HTTPS.

Per impostazione predefinita, il modulo di griglia di eventi convaliderà il certificato server del Sottoscrittore. È possibile ignorare la convalida eseguendo l'override **outbound__webhook__skipServerCertValidation**. I valori possibili sono:

| Valori possibili | Description |
| ----------------  | ------------ |
| true | Non convalidare il certificato server del Sottoscrittore.
| false | Valore predefinito. Convalidare il certificato server del Sottoscrittore.

Se il certificato del Sottoscrittore è autofirmato, per impostazione predefinita il modulo di griglia di eventi rifiuterà tali Sottoscrittori. Per consentire il certificato autofirmato, è possibile eseguire l'override **outbound__webhook__allowUnknownCA**. Nella tabella seguente vengono acquisiti i valori possibili.

| Valori possibili | Description |
| ----------------  | ------------ |
| true | Valore predefinito. Consente di presentare correttamente i certificati autofirmati.
| false | Se vengono presentati certificati autofirmati, le richieste avranno esito negativo.

>[!IMPORTANT]
>Negli scenari di produzione è consigliabile impostare **outbound__webhook__allowUnknownCA** su **false**.

> [!NOTE]
>IoT Edge ambiente genera certificati autofirmati. Si consiglia di generare i certificati emessi dalle autorità di certificazione autorizzate per i carichi di lavoro di produzione e impostare la proprietà **allowUnknownCA** sia in ingresso che in uscita su **false**.

## <a name="summary"></a>Riepilogo

Un modulo di griglia di eventi è **sicuro per impostazione predefinita**. È consigliabile mantenere queste impostazioni predefinite per le distribuzioni di produzione.

Di seguito sono riportati i principi di guida da usare durante la configurazione di:

* Consente solo le richieste HTTPS al modulo.
* Consentire solo l'autenticazione client basata su certificato. Consentire solo i certificati rilasciati da CA note. Non consentire i certificati autofirmati.
* Non consentire l'autenticazione client basata su SASKey.
* Presentare sempre il certificato di identità del modulo griglia di eventi nelle chiamate in uscita.
* Consenti solo i sottoscrittori HTTPS per i tipi di destinazione del webhook.
* Convalidare sempre il certificato server del Sottoscrittore per i tipi di destinazione webhook. Consenti solo i certificati emessi da autorità di certificazione ben note. Non consentire i certificati autofirmati.

Per impostazione predefinita, il modulo griglia di eventi viene distribuito con la configurazione seguente:

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
