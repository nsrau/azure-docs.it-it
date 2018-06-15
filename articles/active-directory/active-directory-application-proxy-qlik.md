---
title: Azure Active Directory Application Proxy e Qlik Sense | Microsoft Docs
description: Attivare il proxy di applicazione nel portale di Azure e installare i connettori per il proxy inverso.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 350a43dbb96e900c48a4207c808add1484237ef6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589084"
---
# <a name="application-proxy-and-qlik-sense"></a>Application Proxy e Qlik Sense 
Azure Active Directory Application Proxy e Qlik Sense hanno collaborato assieme per permettere di usare con facilità l'Application Proxy e fornire l'accesso remoto alla distribuzione Qlik Sense.  

## <a name="prerequisites"></a>prerequisiti 
Nella parte restante di questo scenario si presuppone che siano state eseguite le operazioni seguenti:
 
- [Qlik Sense](https://community.qlik.com/docs/DOC-19822) è stato configurato. 
- [È stato installato un connettore proxy di applicazione](manage-apps/application-proxy-enable.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Pubblicare applicazioni in Azure 
Per pubblicare Qlik Sense è necessario pubblicare due applicazioni in Azure.  

### <a name="application-1"></a>Applicazione n°1: 
Seguire questi passaggi per pubblicare l'app. Per una descrizione più dettagliata dei passaggi 1-8, vedere [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](manage-apps/application-proxy-publish-azure-portal.md). 


1. Accedere al portale di Azure come amministratore globale. 
2. Selezionare **Azure Active Directory** > **Applicazioni aziendali**. 
3. Selezionare **Aggiungi** nella parte superiore del pannello. 
4. Selezionare **Applicazione locale**. 
5.       Compilare i campi obbligatori con le informazioni della nuova app. Usare le seguenti linee guida per le impostazioni: 
    - **URL interno**: questa applicazione deve avere un URL interno, che corrisponde all'URL Qlik Sense stesso. Ad esempio, **https&#58;//demo.qlikemm.com:4244** 
    - **Metodo di autenticazione preliminare**: Azure Active Directory (consigliato ma non obbligatorio) 
1.       Selezionare **Aggiungi** nella parte inferiore del pannello. L'applicazione viene aggiunta e si apre il menu di avvio rapido. 
2.       Nel menu di avvio rapido selezionare **Assegna utente per il test** e aggiungere almeno un utente all'applicazione. Assicurarsi che questo account di test abbia accesso all'applicazione locale. 
3.       Selezionare **Assegna** per salvare l'assegnazione dell'utente di test. 
4.       (Facoltativo) Nel pannello di gestione dell'app selezionare Single Sign-On. Scegliere **Delega vincolata Kerberos** dal menu a discesa e compilare i campi obbligatori in base alla configurazione Qlik. Selezionare **Salva**. 

### <a name="application-2"></a>Applicazione n°2: 
Seguire gli stessi passaggi usati per l'applicazione n°1, con le eccezioni seguenti: 

**Passaggio 5**: l'URL interno deve ora corrispondere all'URL Qlik Sense con la porta di autenticazione usata dall'applicazione. Il valore predefinito è **4244** per HTTPS e 4248 per HTTP. Ad esempio, **https&#58;//demo.qlik.com:4244**</br></br> 
**Passaggio 10:** non configurare SSO e lasciare il **Single Sign-On disabilitato**
 
 
## <a name="testing"></a>Test 
L'applicazione è ora pronta per il test. Accedere all'URL esterno usato per pubblicare Qlik Sense nell'applicazione n°1 e accedere come utente assegnato a entrambe le applicazioni.  

## <a name="next-steps"></a>Passaggi successivi

- [Pubblicare le applicazioni con il proxy di applicazione](manage-apps/application-proxy-publish-azure-portal.md)
- [Uso di connettori del proxy di applicazione](manage-apps/application-proxy-connector-groups.md).
