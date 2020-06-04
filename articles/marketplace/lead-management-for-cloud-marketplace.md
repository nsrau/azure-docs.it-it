---
title: Gestione dei lead per il marketplace commerciale | Azure Marketplace e AppSource
description: Panoramica dei vari argomenti relativi alla pubblicazione di offerte ed elementi tecnici in Azure Marketplace e AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: f8b466dca9f3af55e3c11b39b3fbdac315af3675
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798582"
---
# <a name="lead-management-for-commercial-marketplace"></a>Gestione dei lead per il marketplace commerciale

I clienti costituiscono il fulcro di qualsiasi attività valida. Nella trasformazione odierna delle acquisizioni di prodotti, gli addetti al marketing devono concentrarsi sulla connessione diretta con i clienti e la creazione di una relazione. Ecco perché la generazione di lead di alta qualità è uno strumento fondamentale per il ciclo di vendita. Dopo la presentazione dell'offerta nel [Centro per i partner](https://partner.microsoft.com/), sono disponibili strumenti che consentono di ricevere le informazioni di contatto del cliente a livello di codice immediatamente dopo che il cliente esprime interesse o distribuisce il prodotto nel marketplace. 

## <a name="what-are-leads-in-the-marketplace"></a>Che cosa sono i lead nel marketplace?

I lead provengono dai clienti che manifestano interesse per i prodotti o li distribuiscono dal Marketplace. Sia che si presenti il prodotto in Azure Marketplace o in AppSource, si potranno ricevere i lead dai clienti dopo aver configurato correttamente il prodotto dal sistema CRM all'area delle presentazioni nel Centro per i partner. 

## <a name="how-to-connect-your-crm-system-with-partner-center"></a>Come connettere il sistema CRM con il Centro per i partner

Per iniziare a ricevere i lead, il connettore di gestione lead nel Centro per i partner è progettato in modo da poter essere facilmente collegato con le informazioni del sistema CRM a un elenco dei sistemi CRM disponibili. A questo punto è possibile sfruttare facilmente i lead generati dal marketplace senza un notevole sforzo di progettazione per integrarli con un sistema esterno.

Ecco le istruzioni dettagliate da seguire per connettere ognuna delle possibili destinazioni di lead:

**Dynamics CRM Online**: vedere [Configurare la gestione dei lead per Dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) per istruzioni su come configurare Dynamics CRM Online per ottenere lead.

**Marketo**: vedere [Configurare la gestione dei lead in Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md) per istruzioni sulla configurazione dei lead in Marketo per ottenere lead.

**Salesforce**: vedere [Configurare la gestione dei lead per Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md) per istruzioni sulla configurazione dell'istanza di Salesforce per ottenere lead.

**Tabella di Azure**: vedere [Configurare la gestione dei lead con una tabella di Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) per istruzioni sulla configurazione dell'account di archiviazione di Azure per ottenere lead in una tabella di Azure.

**Endpoint HTTPS**: vedere [Configurare la gestione dei lead con un endpoint HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) per istruzioni sulla configurazione dell'endpoint HTTPS per ottenere lead.

Una volta configurate correttamente la destinazione dei lead e aver premuto Pubblica sull'offerta, verrà convalidata la connessione e inviato un lead di test all'utente. Quando si visualizza l'offerta prima della distribuzione, è inoltre possibile testare la connessione lead quando si tenta di acquisire l'offerta nell'ambiente di anteprima. È importante assicurarsi che le impostazioni dei lead rimangano aggiornate affinché nessun lead venga perso. Pertanto, assicurarsi di aggiornare le connessioni ogni volta che c'è una modifica da parte dell'utente.

### <a name="what-are-the-next-steps"></a>Passaggi successivi

Dopo che è stata eseguita la configurazione tecnica, è necessario incorporare i lead nella strategia di vendita e marketing e nei processi operativi correnti. L'intento è quello di comprendere meglio il processo complessivo di vendita e di collaborare con l'utente per creare lead di alta qualità e dati sufficienti a garantire il successo dell'utente. Ogni commento su come ottimizzare e migliorare i lead inviati con dati aggiuntivi per contribuire ad avere clienti di successo è apprezzato. Sono graditi feedback e suggerimenti che consentano al team di vendita di sviluppare i lead del marketplace in modo ottimale.

## <a name="common-lead-configuration-errors-during-publishing-in-partner-center"></a><a id="publishing-config-errors"></a> Errori comuni di configurazione dei lead durante la pubblicazione nel Centro per i partner

**Non è stato possibile salvare il lead in Dynamics CRM. Controllare le impostazioni dell'account Dynamics CRM. LastCRMError: Non è possibile accedere a Dynamics CRM, LastCRMException:** 

> Se è stata selezionata l'autenticazione di Office 365, verificare che l'account utente e la password siano validi. Se è stato selezionato AAD, verificare che l'ID tenant, l'ID applicazione e la chiave privata dell'applicazione corrispondano ai valori configurati in AAD. Seguire le istruzioni [qui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md). Se il nome utente e la password dell'account sono validi, verificare che l'account abbia accesso a Dynamics 365 e disponga di una licenza (passaggi 11-15 se si usa Azure Active Directory o Impostazioni di sicurezza se si usa un utente di Office). 

**Non è stato possibile salvare il lead in Dynamics CRM. L'utente non ha le autorizzazioni di creazione per l'attributo leadsourcecode nell'entità lead** 

> L'applicazione o l'utente non ha i ruoli di sicurezza su Microsoft Marketplace Lead Writer. Seguire i passaggi 11-15 se si usa Azure Active Directory oppure Impostazioni di sicurezza se si usa un utente di Office [qui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Non è stato possibile salvare il lead in Dynamics CRM tramite AAD. Eccezione: tenant non trovato. Questa situazione può verificarsi se non sono presenti sottoscrizioni attive per il tenant.**  

> L'ID directory specificato nella sezione relativa alla gestione dei lead non corrisponde a una directory valida. Ottenere l'ID directory seguendo le istruzioni del passaggio 2 (in Azure Active Directory, [qui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)).

**Non è stato possibile salvare il lead in Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser non riuscito - nessun ruolo assegnato all'utente.**  

> Risoluzione: assegnare il ruolo di sicurezza a Microsoft Marketplace Lead Writer. Seguire le istruzioni [qui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) in Impostazioni di sicurezza.

**Non è stato possibile salvare il lead in Dynamics CRM tramite AAD. Eccezione: applicazione con identificatore non trovata nella directory** 

> L'ID applicazione specificato nella sezione relativa alla gestione dei lead non corrisponde a un'applicazione valida. Ottenere l'ID applicazione seguendo le istruzioni del passaggio 8 (in Azure Active Directory, [qui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Non è stato possibile salvare il lead in Dynamics CRM tramite AAD. Eccezione: identificatore tenant richiesto non valido e formato di dominio esterno non valido** 

> L'ID directory specificato nella sezione relativa alla gestione dei lead non corrisponde a una directory valida. Ottenere l'ID directory seguendo le istruzioni del passaggio 2 (in Azure Active Directory, [qui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Non è stato possibile salvare il lead in Dynamics CRM tramite AAD. Eccezione: errore di convalida delle credenziali. Viene fornito un segreto client non valido.** 

> Risoluzione: accedere al portale di Azure e verificare se la chiave dell'applicazione corrisponde a quella indicata nel Centro per i partner. Generare la password seguendo le istruzioni del passaggio 10 (in Azure Active Directory, [qui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Non è stato possibile salvare il lead in Dynamics CRM. LastCRMError: si è verificato il timeout del canale di richiesta durante l'attesa di una risposta dopo 00:02:00. Aumentare il valore di timeout passato alla chiamata a Request o aumentare il valore di SendTimeout nell'elemento Binding. È possibile che il tempo allocato a questa operazione fosse incluso in un timeout più lungo.**  

> Risoluzione: accedere al Centro per i partner e in Dettagli vetrina >> Destinazione del lead >> URL verificare che sia specificata un'istanza di Dynamic CRM valida.

## <a name="frequently-asked-questions"></a>Domande frequenti

**Che cosa sono i lead e perché sono importanti per chi pubblica sul Marketplace?** 

I lead sono i clienti che distribuiscono i prodotti dal Marketplace. Sia che il prodotto sia presentato in [Azure Marketplace](https://azuremarketplace.microsoft.com) o in [AppSource](https://appsource.microsoft.com/), si potranno ricevere i lead dei clienti interessati al prodotto se la destinazione dei lead è stata configurata nell'offerta.  

**Dove è possibile ottenere assistenza per configurare la destinazione dei lead?** 

È possibile trovare documentazione in [Ottenere lead](./partner-center-portal/commercial-marketplace-get-customer-leads.md) o inviare un ticket di supporto in [Guida e supporto tecnico](https://aka.ms/marketplacepublishersupport). Selezionare tipo di offerta e gestione dei lead. 

**È necessario configurare una destinazione dei lead per pubblicare un'offerta nel Marketplace?**

Sì, se si sta pubblicando un'app SaaS Contattami o Servizi di consulenza.  
 
**Come è possibile verificare che la configurazione dei lead sia corretta?**

Dopo aver configurato l'offerta e la destinazione dei lead, pubblicare l'offerta. Durante il processo di convalida dei lead, Marketplace invierà un lead di test alla destinazione configurata nell'offerta. 

**Come si trova il lead di test?**

Cercare "MSFT_TEST" nella destinazione dei lead. Ecco un esempio di dati di lead di test: 

company = MSFT_TEST_636573304831318844 

country = US 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

encoding = UTF-8 

encoding = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|\<Nome offerta> 

oid = 00Do0000000ZHog 

phone = 1234567890 

title = MSFT_TEST_636573304831318844 

**Perché per un'offerta attiva non è visualizzato alcun lead?**

I dati di ogni lead vengono passati in campi nella destinazione selezionata. I lead hanno questo formato: **Origine-Azione|Offerta** 

  *Origini:*

    "AzureMarketplace", 
    "AzurePortal", 
    "TestDrive",  
    "SPZA" (acronym for AppSource) 

  *Azioni:*

    "INS" - Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    "PLT" - Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    "DNC" - Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    "Create" - This is inside Azure portal only and is whenever a customer purchases your offer to their account. 

    "StartTestDrive" - This is for Test Drives only and is whenever a customer starts their test drive. 


  *Offerte:*

    "checkpoint.check-point-r77-10sg-byol", 
    "bitnami.openedxcypress", 
    "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a" 

 

  *Ecco un esempio di dati sulle informazioni del cliente*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Per altre informazioni, vedere [Lead Info](./partner-center-portal/commercial-marketplace-get-customer-leads.md) (Informazioni sui lead). 

**È stato configurato un BLOB di Azure come destinazione dei lead. Perché il lead non è visualizzato?** 

Il lead viene scritto solo quando si seleziona l'archivio BLOB di Azure come destinazione. Passare alla tabella di Azure per ricevere il lead in tempo reale.

**È stato ricevuto un messaggio di posta elettronica dal Marketplace, perché non si riesce a trovare il lead nel sistema CRM?**  

È possibile che il dominio di posta elettronica dell'utente finale sia di tipo edu. Per motivi di privacy, le informazioni personali non vengono passate da questo tipo di domini. Inviare un ticket di supporto in [Guida e supporto tecnico](https://aka.ms/marketplacepublishersupport).

**È stato configurato un BLOB di Azure o una tabella di Azure come destinazione dei lead. Perché i lead non sono visualizzati?** 

È possibile accedere al BLOB o alla tabella dal portale di Azure oppure scaricare e installare gratuitamente [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per visualizzare le tabelle e i BLOB del proprio account di archiviazione di Azure. 

**È stata configurata una tabella di Azure come destinazione dei lead. È possibile ricevere una notifica quando viene inviato un nuovo lead dal Marketplace?** 

Sì. Seguire le istruzioni per configurare la tabella di Azure e la funzione nella documentazione disponibile [qui](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md). 

**È stato configurato Salesforce come destinazione dei lead. Perché i lead non sono visualizzati?**

Verificare se il modulo Web-to-Lead (Da Web a lead) è un campo obbligatorio in base a un elenco a discesa. Se lo è, impostarlo su un campo di testo non obbligatorio.  
 
**C'è stato un problema con la destinazione dei lead e non si sono visti alcuni lead. È possibile riceverli tramite posta elettronica?** 

A causa dei criteri relativi alla privacy, non è possibile condividere informazioni sui lead tramite posta elettronica non protetta. 

**È stato configurato il servizio Archiviazione di Azure (BLOB/Tabella) come destinazione dei lead. Quanto costerà?** 

I dati di generazione dei lead sono inferiori a 1 GB per quasi tutti gli editori. Il costo dipenderà dal numero di lead ricevuti. Se ad esempio si ricevono 1.000 lead in un mese, il costo è di circa 50 centesimi. 
