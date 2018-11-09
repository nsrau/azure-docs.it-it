---
title: Cos'è l'insieme di credenziali chiave di Azure? | Microsoft Docs
description: L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Con l'insieme di credenziali chiave di Azure i clienti possono crittografare chiavi e segreti (ad esempio, chiavi di autenticazione, chiavi dell'account di archiviazione, chiavi di crittografia dati, file PFX e password) usando chiavi protette da moduli di protezione hardware (HSM).
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: barclayn
ms.openlocfilehash: d1a6da5d599296a11678ee58cadc42d61296e8e7
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230302"
---
# <a name="what-is-azure-key-vault"></a>Cos'è l'insieme di credenziali chiave di Azure?

Azure Key Vault contribuisce a risolvere i problemi seguenti:
- **Gestione dei segreti**: Azure Key Vault può essere usato per archiviare in modo sicuro e controllare rigorosamente l'accesso a token, password, certificati, chiavi API e altri segreti.
- **Gestione delle chiavi**: Azure Key Vault può essere usato anche come soluzione di gestione delle chiavi. Con Azure Key Vault è semplice creare e controllare le chiavi di crittografia usate per crittografare i dati. 
- **Gestione dei certificati**: Azure Key Vault è anche un servizio che facilita il provisioning, la gestione e la distribuzione dei certificati Secure Sockets Layer/Transport Layer Security (TLS/SSL) pubblici e privati da usare con Azure e le risorse connesse interne. 
- **Archiviazione dei segreti supportati da moduli di protezione hardware**: le chiavi e i segreti possono essere protetti da software o da moduli di protezione hardware certificati FIPS 140-2 livello 2.

## <a name="basic-concepts"></a>Concetti di base

Azure Key Vault è uno strumento per archiviare i segreti e accedervi in modo sicuro. Un segreto è qualsiasi elemento per cui si vuole controllare rigorosamente l'accesso, ad esempio chiavi API, password o certificati. Un **insieme di credenziali** è un gruppo logico di segreti. Per eseguire qualsiasi operazione con Key Vault, è innanzitutto necessario autenticarsi al servizio. 

Esistono 3 modi per eseguire l'autenticazione a Key Vault:

1. **Tramite le [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)**  (**procedura ottimale e consigliata**): quando si distribuisce un'app in una macchina virtuale in Azure, è possibile assegnare un'identità alla macchina virtuale che dispone dell'accesso a Key Vault. È anche possibile assegnare identità ad altre risorse di Azure elencate in [questo articolo](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Il vantaggio di questo approccio è che l'app o il servizio non gestisce la rotazione del primo segreto. Azure ruota automaticamente l'identità. 
2. **Tramite l'entità servizio e il certificato**: la seconda opzione consiste nell'usare un'entità servizio e un certificato associato che abbia accesso a Key Vault. L'onere di rotazione del certificato riguarda il proprietario dell'applicazione o lo sviluppatore e non è pertanto consigliato.
3. **Tramite l'entità servizio e il segreto**: la terza opzione (non consigliata) consiste nell'uso di un'entità servizio e di un segreto per eseguire l'autenticazione in Key Vault.

> [!NOTE]
> Non usare l'opzione 3 precedente in quanto è difficile ruotare automaticamente il segreto del bootstrap usato per eseguire l'autenticazione in Key Vault.

Di seguito sono riportati alcuni termini chiave:
- **Tenant**: un tenant è l'organizzazione che possiede e gestisce una specifica istanza dei servizi cloud Microsoft. Il termine viene spesso usato in particolare per fare riferimento al set di servizi di Azure e Office 365 per un'organizzazione.
- **Proprietario dell'insieme di credenziali**: il proprietario può creare un insieme di credenziali delle chiavi e ottenerne un accesso e controllo completi. Il proprietario dell'insieme di credenziali può anche configurare il controllo per registrare chi accede a segreti e chiavi. Gli amministratori possono controllare il ciclo di vita delle chiavi. Possono passare a una nuova versione della chiave, eseguirne il backup e svolgere attività correlate.
- **Consumer dell'insieme di credenziali**: quando il proprietario dell'insieme di credenziali gli concede l'accesso, il consumer può eseguire azioni sulle risorse nell'insieme di credenziali delle chiavi. Le azioni disponibili dipendono dalle autorizzazioni concesse.
- **Risorsa**: una risorsa è un elemento gestibile disponibile tramite Azure. Alcune risorse comuni sono le macchine virtuali, gli account di archiviazione, le app Web, i database e le reti virtuali, ma ne esistono molte altre.
- **Gruppo di risorse**: un gruppo di risorse è un contenitore con risorse correlate per una soluzione di Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione.
- **Entità servizio**: un'entità servizio di Azure è un'identità di sicurezza usata da app, servizi e strumenti di automazione creati dall'utente per accedere a risorse di Azure specifiche. Può essere considerata come una "identità utente" (nome utente e password o certificato) con un ruolo specifico e autorizzazioni attentamente controllate. A differenza di un'identità utente generica, un'entità servizio dovrà eseguire solo operazioni specifiche. Se le viene concesso solo il livello minimo di autorizzazioni necessarie per eseguire le attività di gestione, un'entità servizio migliora la sicurezza.
- **[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md)**: Azure AD è il servizio Active Directory per un tenant. Ogni directory dispone di uno o più domini. A una directory possono essere associate molte sottoscrizioni, ma un solo tenant. 
- **ID tenant di Azure**: un ID tenant è un modo univoco per identificare un'istanza di Azure AD all'interno di una sottoscrizione di Azure.
- **Identità gestita per le risorse di Azure**: Azure Key Vault consente di archiviare in modo sicuro le credenziali e altre chiavi e segreti, ma è necessario autenticare il codice in Key Vault per recuperarli. L'uso di un'identità gestita consente di risolvere il problema in maniera più semplice, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure AD. È possibile usare questa identità per l'autenticazione in Key Vault o in qualsiasi servizio che supporti l'autenticazione di Azure AD, senza dover inserire le credenziali nel codice. Per altre informazioni, vedere l'immagine seguente e la [panoramica delle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagramma sul funzionamento delle identità gestite per le risorse di Azure](./media/key-vault-whatis/msi.png)

## <a name="key-vault-roles"></a>Ruoli dell'insieme di credenziali delle chiavi

La seguente tabella permette di capire meglio come l'insieme di credenziali chiave aiuti a soddisfare le esigenze degli sviluppatori e degli amministratori della sicurezza.

| Ruolo | Presentazione del problema | Soluzione offerta dall'insieme di credenziali chiave di Azure |
| --- | --- | --- |
| Sviluppatore di un'applicazione Azure |"Voglio scrivere un'applicazione per Azure che usi chiavi per la firma e la crittografia, che però dovranno essere esterne all'applicazione, in modo che la soluzione sia adatta a un'applicazione geograficamente distribuita. <br/><br/>Voglio che queste chiavi e questi segreti siano protetti, senza dover scrivere manualmente il codice, e voglio poterli usare facilmente dall'applicazione ottenendo prestazioni ottimali." |√ Le chiavi vengono archiviate in un insieme di credenziali e richiamate dall'URI quando è necessario.<br/><br/> √ Le chiavi vengono protette da Azure con algoritmi standard del settore, lunghezze delle chiavi e moduli di protezione hardware.<br/><br/> √ Le chiavi vengono elaborate in moduli di protezione hardware che risiedono negli stessi data center di Azure in cui si trovano le applicazioni. In questo modo si ottiene una migliore affidabilità e una latenza ridotta rispetto a chiavi che si trovano in una posizione diversa, ad esempio in locale. |
| Sviluppatore di software come un servizio (SaaS) |"Non voglio essere in alcun modo responsabile delle chiavi e dei segreti dei tenant dei miei clienti. <br/><br/>Voglio che siano i clienti a possedere e gestire le chiavi per potermi concentrare su ciò che so fare meglio, ovvero fornire le principali funzionalità del software." |√ I clienti possono importare le loro chiavi in Azure e gestirle. Quando un'applicazione SaaS deve eseguire operazioni di crittografia usando le chiavi dei clienti, è l'insieme di credenziali delle chiavi a eseguirle per conto dell'applicazione. L'applicazione non vede le chiavi dei clienti. |
| Responsabile della sicurezza |"Voglio la certezza che le applicazioni siano conformi ai moduli di protezione hardware FIPS 140-2 livello 2 per la gestione delle chiavi protetta. <br/><br/>Voglio assicurarmi che la mia organizzazione abbia il controllo del ciclo di vita delle chiavi e possa monitorare l'utilizzo delle chiavi. <br/><br/>Anche se usiamo più servizi e risorse di Azure, voglio gestire le chiavi da una sola posizione in Azure. |√ I moduli di protezione hardware hanno la certificazione FIPS 140-2 livello 2.<br/><br/>√ L'insieme di credenziali delle chiavi è progettato in modo che Microsoft non possa vedere o estrarre le chiavi.<br/><br/>√ L'utilizzo delle chiavi viene registrato quasi in tempo reale.<br/><br/>√ L'insieme di credenziali offre un'unica interfaccia, indipendentemente dal numero di insiemi di credenziali disponibili in Azure, dalle aree supportate e dalle applicazioni che li usano. |

Chiunque abbia una sottoscrizione di Azure può creare e usare insiemi di credenziali delle chiavi. Anche se l'insieme di credenziali chiave è un vantaggio per sviluppatori e amministratori della sicurezza, può essere implementato e gestito dall'amministratore di un'organizzazione che gestisce altri servizi di Azure per un'organizzazione. Ad esempio, questo amministratore può accedere con una sottoscrizione di Azure, creare un insieme di credenziali per l'organizzazione in cui archiviare le chiavi e quindi essere responsabile di attività operative, ad esempio:

* Creare o importare una chiave o un segreto
* Revocare o eliminare una chiave o un segreto
* Autorizzare gli utenti o le applicazioni per l'accesso all'insieme di credenziali delle chiavi, per consentire la gestione o l'uso delle chiavi e dei segreti corrispondenti
* Configurare l'utilizzo delle chiavi (ad esempio, la firma o la crittografia)
* Monitorare l'utilizzo delle chiavi

L'amministratore quindi fornirebbe agli sviluppatori gli URI da chiamare dalle applicazioni e fornirebbe all'amministrazione della sicurezza le informazioni sulla registrazione dell'utilizzo delle chiavi. 

![Panoramica dell'insieme di credenziali chiave di Azure][1]

Gli sviluppatori possono gestire le chiavi anche direttamente, usando le API. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure](key-vault-developers-guide.md).

## <a name="next-steps"></a>Passaggi successivi

Per un'esercitazione introduttiva per gli amministratori, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](key-vault-get-started.md).

Per altre informazioni sulla registrazione dell'utilizzo per l'insieme di credenziali delle chiavi, vedere [Registrazione dell'insieme di credenziali delle chiavi di Azure](key-vault-logging.md).

Per altre informazioni sull'uso di chiavi e segreti con Azure Key Vault, vedere [Informazioni su chiavi, segreti e certificati](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
L'insieme di credenziali delle chiavi di Azure è disponibile nella maggior parte delle aree. Per altre informazioni, vedere la [pagina Insieme di credenziali delle chiavi - Prezzi](https://azure.microsoft.com/pricing/details/key-vault/).
