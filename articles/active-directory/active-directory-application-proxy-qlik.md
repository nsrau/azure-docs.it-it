---
title: Azure Active Directory Application Proxy e Qlik Sense | Microsoft Docs
description: Attivare il proxy di applicazione nel portale di Azure e installare i connettori per il proxy inverso.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/26/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 331f8ed2e77a076dd8969dc37add1cdeafc852dc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="application-proxy-and-qlik-sense"></a>Application Proxy e Qlik Sense 
Azure Active Directory Application Proxy e Qlik Sense hanno collaborato assieme per permettere di usare con facilità l'Application Proxy e fornire l'accesso remoto alla distribuzione Qlik Sense.  

## <a name="prerequisites"></a>prerequisiti 
Nella parte restante di questo scenario si presuppone che siano state eseguite le operazioni seguenti:
 
- [Qlik Sense](https://community.qlik.com/docs/DOC-19822) è stato configurato. 
- È stato installato un connettore proxy di applicazione 

## <a name="install-an-application-proxy-connector"></a>Installare un connettore proxy di applicazione 
Se il proxy di applicazione è già attivato e si ha già un connettore installato, è possibile ignorare questa sezione e passare a [Aggiungere l'app in Azure AD con il proxy dell'applicazione](application-proxy-ping-access.md). 

Il connettore del proxy di applicazione è un servizio di Windows Server che indirizza il traffico dai dipendenti remoti alle app pubblicate. Per istruzioni di installazione più dettagliate, vedere [Abilitare il proxy di applicazione nel portale di Azure](active-directory-application-proxy-enable.md). 


1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale. 
2. Selezionare Azure Active Directory > Proxy di applicazione. 
3. Selezionare Scarica connettore per avviare il download del connettore del proxy di applicazione. Seguire le istruzioni di installazione. 
 
>[!NOTE]
>Scaricando il connettore si attiverà automaticamente il proxy di applicazione per la directory, ma se così non fosse, è possibile selezionare **Abilita proxy di applicazione**. 
 
## <a name="publish-your-applications-in-azure"></a>Pubblicare applicazioni in Azure 
Per pubblicare Qlik Sense è necessario pubblicare due applicazioni in Azure.  

### <a name="application-1"></a>Applicazione n°1: 
Seguire questi passaggi per pubblicare l'app. Per una descrizione più dettagliata dei passaggi 1-8, vedere [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](application-proxy-publish-azure-portal.md). 


1. Se non lo si è fatto nell'ultima sezione, accedere al portale di Azure come amministratore globale. 
2. Selezionare **Azure Active Directory** > **Applicazioni aziendali**. 
3. Selezionare **Aggiungi** nella parte superiore del pannello. 
4. Selezionare **Applicazione locale**. 
5.       Compilare i campi obbligatori con le informazioni della nuova app. Usare le seguenti linee guida per le impostazioni: 
    - **URL interno**: questa applicazione deve avere un URL interno, che corrisponde all'URL Qlik Sense stesso. Ad esempio, **https&#58;//demo.qlikemm.com** 
    - **Metodo di autenticazione preliminare**: Azure Active Directory (consigliato ma non obbligatorio) 
1.       Selezionare **Aggiungi** nella parte inferiore del pannello. L'applicazione viene aggiunta e si apre il menu di avvio rapido. 
2.       Nel menu di avvio rapido selezionare **Assegna utente per il test** e aggiungere almeno un utente all'applicazione. Assicurarsi che questo account di test abbia accesso all'applicazione locale. 
3.       Selezionare **Assegna** per salvare l'assegnazione dell'utente di test. 
4.       (Facoltativo) Nel pannello di gestione dell'app selezionare Single Sign-On. Scegliere **Delega vincolata Kerberos** dal menu a discesa e compilare i campi obbligatori in base alla configurazione Qlik. Selezionare **Salva**. 

### <a name="application-2"></a>Applicazione n°2: 
Seguire gli stessi passaggi usati per l'applicazione n°1, con le eccezioni seguenti: 

**Passaggio 5**: l'URL interno deve ora corrispondere all'URL Qlik Sense con la porta di autenticazione usata dall'applicazione. Il valore predefinito è **4244** per HTTPS e 4248 per HTTP. Ad esempio: **https&#58;//demo.qlik.com:4244** 
**Passaggio 10:** non configurare SSO e lasciare il **Single Sign-On disabilitato**
 
 
## <a name="testing"></a>Test 
L'applicazione è ora pronta per il test. Accedere all'URL esterno usato per pubblicare Qlik Sense nell'applicazione n°1 e accedere come utente assegnato a entrambe le applicazioni.  

## <a name="next-steps"></a>Passaggi successivi

- [Pubblicare le applicazioni con il proxy di applicazione](application-proxy-publish-azure-portal.md)
- [Uso di connettori del proxy di applicazione](active-directory-application-proxy-connectors-azure-portal.md).
