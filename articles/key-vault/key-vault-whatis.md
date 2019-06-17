---
title: Cos'è l'insieme di credenziali chiave di Azure? | Microsoft Docs
description: Informazioni su come Azure Key Vault protegge le chiavi crittografiche e segreti che le applicazioni e servizi cloud usano.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 43794b8ef4e0272362c7695eda75f5af36a77d1e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683468"
---
# <a name="what-is-azure-key-vault"></a>Cos'è l'insieme di credenziali chiave di Azure?

Servizi e applicazioni cloud usano le chiavi di crittografia e proteggere i segreti per mantenere le informazioni. Azure Key Vault protegge le chiavi e segreti. Quando si usa Key Vault, è possibile crittografare le chiavi di autenticazione, chiavi dell'account di archiviazione, le chiavi DEK, file PFX e password usando chiavi protette da moduli di protezione hardware (HSM).

Key Vault contribuisce a risolvere i problemi seguenti:

- **Gestione dei segreti**: Archiviare in modo sicuro e controllare rigorosamente l'accesso al token, password, certificati, chiavi API e altri segreti.
- **Gestione delle chiavi**: Creare e controllare le chiavi di crittografia che crittografa i dati. 
- **Gestione dei certificati**: Effettuare il provisioning, gestire e distribuire certificati Secure Socket Layer/Transport Layer Security (TLS/SSL) pubblici e privati da usare con Azure e le risorse connesse interne. 
- **Store segreti supportati da moduli di protezione hardware**: Utilizzare software o FIPS 140-2 livello 2 convalidati moduli di protezione hardware per proteggere le chiavi e segreti.

## <a name="basic-concepts"></a>Concetti di base

Azure Key Vault è uno strumento per archiviare i segreti e accedervi in modo sicuro. Un segreto è qualsiasi elemento per cui si vuole controllare rigorosamente l'accesso, ad esempio chiavi API, password o certificati. Un insieme di credenziali è un gruppo logico di segreti.

Ecco altri termini importanti:

- **Tenant**: un tenant è l'organizzazione che possiede e gestisce una specifica istanza dei servizi cloud Microsoft. In genere utilizzato per fare riferimento al set di servizi di Azure e Office 365 per un'organizzazione.

- **Proprietario dell'insieme di credenziali**: il proprietario di un insieme di credenziali può creare un insieme di credenziali delle chiavi e ottenerne l'accesso e il controllo completi. Il proprietario dell'insieme di credenziali può anche configurare il controllo per registrare chi accede a segreti e chiavi. Gli amministratori possono controllare il ciclo di vita delle chiavi. Possono passare a una nuova versione della chiave, eseguirne il backup e svolgere attività correlate.

- **Consumer dell'insieme di credenziali**: quando il proprietario dell'insieme di credenziali concede l'accesso a un consumer, quest'ultimo può eseguire azioni sugli asset all'interno dell'insieme di credenziali delle chiavi. Le azioni disponibili dipendono dalle autorizzazioni concesse.

- **Resource** (Risorsa): una risorsa è un elemento gestibile disponibile tramite Azure. Esempi comuni sono macchine virtuali, account di archiviazione, app web, database e rete virtuale. Esistono molti altri.

- **Gruppo di risorse**: Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione.

- **Entità servizio**: Un'entità servizio di Azure è un'identità di sicurezza che le app create dall'utente, servizi e strumenti di automazione da usare per accedere alle risorse di Azure specifiche. Considerarlo come una "identità utente" (nome utente e password o certificato) con un ruolo specifico e autorizzazioni attentamente controllate. A differenza di un'identità utente generica, un'entità servizio dovrà eseguire solo operazioni specifiche. Migliora la sicurezza se si concede solo il livello di autorizzazioni minime necessarie per eseguire le attività di gestione.

- [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md): Azure AD è il servizio Active Directory di un tenant. Ogni directory dispone di uno o più domini. A una directory possono essere associate molte sottoscrizioni, ma un solo tenant.

- **ID tenant di Azure**: un ID tenant è un modo univoco per identificare un'istanza di Azure AD all'interno di una sottoscrizione di Azure.

- **Le identità gestita**: Azure Key Vault consente di archiviare in modo sicuro le credenziali e altre chiavi e segreti, ma è necessario autenticare il codice in Key Vault per recuperarle. L'uso di un'identità gestita consente di risolvere il problema in maniera più semplice, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure AD. È possibile usare questa identità per l'autenticazione in Key Vault o in qualsiasi servizio che supporti l'autenticazione di Azure AD, senza dover inserire le credenziali nel codice. Per altre informazioni, vedere la seguente figura e la [panoramica dell'identità gestita per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagramma delle identità come gestita per il lavoro di risorse di Azure](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Authentication
Per eseguire qualsiasi operazione con Key Vault, è necessario eseguire l'autenticazione ad esso. Esistono tre modi per eseguire l'autenticazione in Key Vault:

- [Identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview): Quando si distribuisce un'app in una macchina virtuale in Azure, è possibile assegnare un'identità per la macchina virtuale che dispone dell'accesso a Key Vault. È anche possibile assegnare le identità allo [altre risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Il vantaggio di questo approccio è che l'app o il servizio non gestisce la rotazione della chiave privata prima. Azure ruota automaticamente l'identità. Questo approccio è consigliato come procedura consigliata. 
- **Entità e certificato del servizio**: È possibile usare un'entità servizio e un certificato associato che abbia accesso a Key Vault. Questo approccio non è consigliabile perché il proprietario dell'applicazione o lo sviluppatore deve ruotare il certificato.
- **Entità servizio e il segreto**: Anche se per l'autenticazione in Key Vault, è possibile usare un'entità servizio e un segreto, non è consigliabile. È difficile automaticamente ruotare il segreto di bootstrap che consente di eseguire l'autenticazione in Key Vault.


## <a name="key-vault-roles"></a>Ruoli dell'insieme di credenziali delle chiavi

La seguente tabella permette di capire meglio come l'insieme di credenziali chiave aiuti a soddisfare le esigenze degli sviluppatori e degli amministratori della sicurezza.

| Ruolo | Presentazione del problema | Soluzione offerta dall'insieme di credenziali chiave di Azure |
| --- | --- | --- |
| Sviluppatore di un'applicazione Azure |"Voglio scrivere un'applicazione per Azure che usa le chiavi di firma e crittografia. Ma voglio dovranno essere esterne all'applicazione in modo che la soluzione sia adatta a un'applicazione geograficamente distribuita. <br/><br/>Voglio che queste chiavi e questi segreti siano protetti, senza dover scrivere manualmente il codice, e voglio poterli usare facilmente dall'applicazione ottenendo prestazioni ottimali." |√ Le chiavi vengono archiviate in un insieme di credenziali e richiamate dall'URI quando è necessario.<br/><br/> √ Le chiavi vengono protette da Azure con algoritmi standard del settore, lunghezze delle chiavi e moduli di protezione hardware.<br/><br/> √ Le chiavi vengono elaborate in moduli di protezione hardware che risiedono negli stessi data center di Azure in cui si trovano le applicazioni. In questo modo si ottiene una migliore affidabilità e una latenza ridotta rispetto a chiavi che si trovano in una posizione diversa, ad esempio in locale. |
| Sviluppatore di software come un servizio (SaaS) |"Non voglio essere in alcun modo responsabile delle chiavi e dei segreti dei tenant dei miei clienti. <br/><br/>Voglio che siano i clienti a possedere e gestire le chiavi per potermi concentrare su ciò che so fare meglio, ovvero fornire le principali funzionalità del software." |√ I clienti possono importare le loro chiavi in Azure e gestirle. Quando un'applicazione SaaS deve eseguire operazioni di crittografia con chiavi dei clienti, Key Vault esegue queste operazioni per conto dell'applicazione. L'applicazione non vede le chiavi dei clienti. |
| Responsabile della sicurezza |"Voglio la certezza che le applicazioni siano conformi ai moduli di protezione hardware FIPS 140-2 livello 2 per la gestione delle chiavi protetta. <br/><br/>Voglio assicurarmi che la mia organizzazione abbia il controllo del ciclo di vita delle chiavi e possa monitorare l'utilizzo delle chiavi. <br/><br/>Anche se usiamo più servizi e risorse di Azure, voglio gestire le chiavi da una sola posizione in Azure. |√ I moduli di protezione hardware hanno la certificazione FIPS 140-2 livello 2.<br/><br/>√ L'insieme di credenziali delle chiavi è progettato in modo che Microsoft non possa vedere o estrarre le chiavi.<br/><br/>√ L'utilizzo delle chiavi viene registrato quasi in tempo reale.<br/><br/>√ L'insieme di credenziali offre un'unica interfaccia, indipendentemente dal numero di insiemi di credenziali disponibili in Azure, dalle aree supportate e dalle applicazioni che li usano. |

Chiunque abbia una sottoscrizione di Azure può creare e usare insiemi di credenziali delle chiavi. Anche se l'insieme di credenziali chiave vantaggio per sviluppatori e amministratori della sicurezza, può essere implementato e gestito da un amministratore dell'organizzazione che gestisce altri servizi di Azure. Ad esempio, l'amministratore può accedere con una sottoscrizione di Azure, creare un insieme di credenziali per l'organizzazione in cui archiviare le chiavi e quindi essere responsabile per le attività operative simili ai seguenti:

- Creare o importare una chiave o un segreto
- Revocare o eliminare una chiave o un segreto
- Autorizzare gli utenti o le applicazioni per l'accesso all'insieme di credenziali delle chiavi, per consentire la gestione o l'uso delle chiavi e dei segreti corrispondenti
- Configurare l'utilizzo delle chiavi (ad esempio, la firma o la crittografia)
- Monitorare l'utilizzo delle chiavi

Quindi, l'amministratore offre agli sviluppatori gli URI da chiamare dalle rispettive applicazioni. Questo amministratore offre inoltre le informazioni di registrazione di utilizzo delle chiavi all'amministratore della sicurezza. 

![Panoramica del funzionamento di Azure Key Vault][1]

Gli sviluppatori possono gestire le chiavi anche direttamente, usando le API. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure](key-vault-developers-guide.md).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [proteggere l'insieme di credenziali](key-vault-secure-your-key-vault.md).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
L'insieme di credenziali delle chiavi di Azure è disponibile nella maggior parte delle aree. Per altre informazioni, vedere la [pagina Insieme di credenziali delle chiavi - Prezzi](https://azure.microsoft.com/pricing/details/key-vault/).
