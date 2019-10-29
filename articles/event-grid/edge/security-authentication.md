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
ms.openlocfilehash: 77b4b265b2e993ccdbc9e07fd2dab5a37ed22a6b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992158"
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

Per impostazione predefinita, sono consentite solo le comunicazioni HTTPS. È possibile eseguire l'override di questo comportamento tramite la configurazione in **ingresso: serverAuth: tlsPolicy** . Nella tabella seguente vengono acquisiti i valori possibili di questa proprietà.

| Valori possibili | Descrizione |
| ---------------- | ------------ |
| Strict | Default. Abilita solo HTTPS
| Enabled | Abilita sia HTTP che HTTPS
| Disabled | Abilita solo HTTP

## <a name="inbound-client-authentication"></a>Autenticazione client in ingresso

I client sono entità che eseguono operazioni di gestione e/o di Runtime. I client possono essere costituiti da altri moduli IoT Edge e da applicazioni diverse.

Il modulo di griglia di eventi supporta due tipi di autenticazione client:-

* Firma di accesso condiviso (SAS) basata su chiavi
* Basato su certificati

Per impostazione predefinita, il modulo di griglia di eventi è configurato per accettare solo l'autenticazione basata sui certificati. All'avvio, il modulo di griglia di eventi recupera "TrustBundle" da IoT Edge daemon di sicurezza e lo usa per convalidare qualsiasi certificato client. I certificati client che non vengono risolti in questa catena verranno rifiutati con `UnAuthorized`.

### <a name="certificate-based-client-authentication"></a>Autenticazione client basata su certificati

L'autenticazione basata su certificati è attiva per impostazione predefinita. È possibile scegliere di disabilitare l'autenticazione basata su certificati tramite la proprietà in **ingresso: clientAuth: clientCert: Enabled**. Nella tabella seguente vengono acquisiti i valori possibili.

| Valori possibili | Descrizione |
| ----------------  | ------------ |
| true | Default. Richiede che tutte le richieste nel modulo di griglia di eventi presentino un certificato client. Inoltre, sarà necessario configurare in **ingresso: clientAuth: clientCert: origine**.
| false | Non forzare un client a presentare il certificato.

La tabella seguente acquisisce i possibili valori per **inbound: clientAuth: clientCert: Source**

| Valori possibili | Descrizione |
| ---------------- | ------------ |
| IoT Edge | Default. Usa il Trustbundle di IoT Edge per convalidare tutti i certificati client.

Se un client presenta un autofirmato, per impostazione predefinita il modulo di griglia di eventi rifiuterà tali richieste. È possibile scegliere di consentire certificati client autofirmati tramite la proprietà **inbound: clientAuth: clientCert: allowUnknownCA** . Nella tabella seguente vengono acquisiti i valori possibili.

| Valori possibili | Descrizione |
| ----------------  | ------------|
| true | Default. Consente di presentare correttamente i certificati autofirmati.
| false | Se vengono presentati certificati autofirmati, le richieste avranno esito negativo.

>[!IMPORTANT]
>Negli scenari di produzione può essere opportuno impostare **inbound: clientAuth: clientCert: allowUnknownCA** su **false**.

### <a name="sas-key-based-client-authentication"></a>Autenticazione client basata su chiave SAS

Oltre all'autenticazione basata su certificati, il modulo di griglia di eventi può anche eseguire l'autenticazione basata su chiave SAS. La chiave SAS è come un segreto configurato nel modulo di griglia di eventi che deve usare per convalidare tutte le chiamate in ingresso. I client devono specificare il segreto nell'intestazione HTTP "AEG-SAS-Key". La richiesta verrà rifiutata con `UnAuthorized` se non corrisponde.

La configurazione per controllare l'autenticazione basata su chiave SAS è in **ingresso: clientAuth: sasKeys: Enabled**.

| Valori possibili | Descrizione  |
| ----------------  | ------------ |
| true | Consente l'autenticazione basata su chiave SAS. Richiede in **ingresso: clientAuth: sasKeys: key1** o in **ingresso: clientAuth: sasKeys: Key2**
| false | Default. L'autenticazione basata su chiave SAS è disabilitata.

 in **ingresso: clientAuth: sasKeys: key1** e in **ingresso: clientAuth: sasKeys: Key2** sono le chiavi che consentono di configurare il modulo di griglia di eventi per verificare la presenza di richieste in ingresso. Almeno una delle chiavi deve essere configurata. Il client che effettua la richiesta dovrà presentare la chiave come parte dell'intestazione della richiesta "**AEG-SAS-Key**". Se entrambe le chiavi sono configurate, il client può presentare uno dei due tasti.

> [!NOTE]
>È possibile configurare entrambi i metodi di autenticazione. In tal caso, la chiave di firma di accesso condiviso viene controllata per prima e solo se l'operazione non riesce, viene eseguita l'autenticazione basata sui certificati. Affinché una richiesta abbia esito positivo, solo uno dei metodi di autenticazione deve avere esito positivo.

## <a name="outbound-client-authentication"></a>Autenticazione client in uscita

Il client nel contesto in uscita fa riferimento al modulo di griglia di eventi. L'operazione eseguita sta distribuendo gli eventi ai sottoscrittori. I moduli di sottoscrizione sono considerati server.

A ogni modulo IoT Edge viene assegnato un certificato di identità dal daemon di sicurezza della IoT Edge. Viene usato il certificato di identità per le chiamate in uscita. Alla scadenza, il modulo viene aggiornato automaticamente con un nuovo certificato dal daemon di sicurezza IoT Edge.

La configurazione per controllare l'autenticazione client in uscita è in **uscita: clientAuth: clientCert: Enabled**.

| Valori possibili | Descrizione |
| ----------------  | ------------ |
| true | Default. Richiede che tutte le richieste in uscita dal modulo griglia di eventi presentino un certificato. Deve configurare in **uscita: clientAuth: clientCert: Source**.
| false | Non richiedere il modulo di griglia di eventi per presentare il certificato.

La configurazione che controlla l'origine del certificato è in **uscita: clientAuth: clientCert: Source**.

| Valori possibili | Descrizione |
| ---------------- | ------------ |
| IoT Edge | Default. Usa il certificato di identità del modulo configurato da IoT Edge daemon di sicurezza.

### <a name="outbound-server-authentication"></a>Autenticazione server in uscita

Uno dei tipi di destinazione di un Sottoscrittore di griglia di eventi è "webhook". Per impostazione predefinita, vengono accettati solo endpoint HTTPS per tali Sottoscrittori.

Configurazione per controllare i criteri di destinazione del webhook in **uscita: webhook: httpsOnly**.

| Valori possibili | Descrizione |
| ----------------  | ------------ |
| true | Default. Consente solo i sottoscrittori con endpoint HTTPS.
| false | Consente ai sottoscrittori con endpoint HTTP o HTTPS.

Per impostazione predefinita, il modulo di griglia di eventi convaliderà il certificato server del Sottoscrittore. È possibile ignorare la convalida eseguendo l'override in **uscita: webhook: skipServerCertValidation**. I valori possibili sono:-

| Valori possibili | Descrizione |
| ----------------  | ------------ |
| true | Non convalidare il certificato server del Sottoscrittore.
| false | Default. Convalidare il certificato server del Sottoscrittore.

Se il certificato del Sottoscrittore è autofirmato, per impostazione predefinita il modulo di griglia di eventi rifiuterà tali Sottoscrittori. Per consentire il certificato autofirmato, è possibile eseguire l'override in **uscita: webhook: allowUnknownCA**. Nella tabella seguente vengono acquisiti i valori possibili.

| Valori possibili | Descrizione |
| ----------------  | ------------ |
| true | Default. Consente di presentare correttamente i certificati autofirmati.
| false | Se vengono presentati certificati autofirmati, le richieste avranno esito negativo.

>[!IMPORTANT]
>Negli scenari di produzione è consigliabile impostare in **uscita: webhook: allowUnknownCA** su **false**.

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

Per impostazione predefinita, il modulo griglia di eventi viene distribuito con la configurazione seguente:-

 ```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
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
