---
title: Cos'è l'insieme di credenziali chiave di Azure? | Microsoft Docs
description: Informazioni su come Azure Key Vault protegge le chiavi crittografiche e i segreti usati dalle applicazioni e dai servizi cloud.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 2786ec387d528e1593e2687d906060f8a2673a8c
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934469"
---
# <a name="what-is-azure-key-vault"></a>Cos'è l'insieme di credenziali chiave di Azure?

Le applicazioni e i servizi cloud usano chiavi crittografiche e segreti per proteggere le informazioni. Azure Key Vault protegge le chiavi e i segreti. Quando si usa Key Vault, è possibile crittografare le chiavi di autenticazione, le chiavi dell'account di archiviazione, le chiavi di crittografia dei dati, i file PFX e le password usando chiavi protette da moduli di protezione hardware (HSM).

Key Vault contribuisce a risolvere i problemi seguenti:

- **Gestione dei segreti**: Archiviare in modo sicuro e controllare rigorosamente l'accesso a token, password, certificati, chiavi API e altri segreti.
- **Gestione delle chiavi**: Creare e controllare le chiavi di crittografia che crittografano i dati. 
- **Gestione dei certificati**: Effettuare il provisioning, la gestione e la distribuzione di certificati Secure Sockets Layer/Transport Layer Security (SSL/TLS) pubblici e privati per l'uso con Azure e le risorse connesse interne. 
- **Archiviare i segreti supportati da HSM**: Usare il software o FIPS 140-2 livello 2 convalidato HSM per proteggere i segreti e le chiavi.

## <a name="basic-concepts"></a>Concetti di base

Azure Key Vault è uno strumento per archiviare i segreti e accedervi in modo sicuro. Un segreto è qualsiasi elemento per cui si vuole controllare rigorosamente l'accesso, ad esempio chiavi API, password o certificati. Un insieme di credenziali è un gruppo logico di segreti.

Ecco altri termini importanti:

- **Tenant**: un tenant è l'organizzazione che possiede e gestisce una specifica istanza dei servizi cloud Microsoft. Viene spesso usato per fare riferimento al set di servizi di Azure e Office 365 per un'organizzazione.

- **Proprietario dell'insieme di credenziali**: il proprietario di un insieme di credenziali può creare un insieme di credenziali delle chiavi e ottenerne l'accesso e il controllo completi. Il proprietario dell'insieme di credenziali può anche configurare il controllo per registrare chi accede a segreti e chiavi. Gli amministratori possono controllare il ciclo di vita delle chiavi. Possono passare a una nuova versione della chiave, eseguirne il backup e svolgere attività correlate.

- **Consumer dell'insieme di credenziali**: quando il proprietario dell'insieme di credenziali concede l'accesso a un consumer, quest'ultimo può eseguire azioni sugli asset all'interno dell'insieme di credenziali delle chiavi. Le azioni disponibili dipendono dalle autorizzazioni concesse.

- **Resource** (Risorsa): una risorsa è un elemento gestibile disponibile tramite Azure. Esempi comuni sono la macchina virtuale, l'account di archiviazione, l'app Web, il database e la rete virtuale. Sono disponibili molte altre.

- **Gruppo di risorse**: Un gruppo di risorse è un contenitore che include le risorse correlate per una soluzione di Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione.

- **Entità servizio**: Un'entità servizio di Azure è un'identità di sicurezza usata da app, servizi e strumenti di automazione creati dall'utente per accedere a risorse di Azure specifiche. È possibile considerarlo come una "identità utente" (nome utente e password o certificato) con un ruolo specifico e autorizzazioni strettamente controllate. A differenza di un'identità utente generica, un'entità servizio dovrà eseguire solo operazioni specifiche. Migliora la sicurezza se si concede solo il livello di autorizzazione minimo necessario per eseguire le attività di gestione.

- [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md): Azure AD è il servizio Active Directory di un tenant. Ogni directory dispone di uno o più domini. A una directory possono essere associate molte sottoscrizioni, ma un solo tenant.

- **ID tenant di Azure**: un ID tenant è un modo univoco per identificare un'istanza di Azure AD all'interno di una sottoscrizione di Azure.

- **Identità gestite**: Azure Key Vault consente di archiviare in modo sicuro le credenziali e altre chiavi e segreti, ma è necessario autenticare il codice in Key Vault per recuperarle. L'uso di un'identità gestita consente di risolvere il problema in maniera più semplice, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure AD. È possibile usare questa identità per l'autenticazione in Key Vault o in qualsiasi servizio che supporti l'autenticazione di Azure AD, senza dover inserire le credenziali nel codice. Per altre informazioni, vedere la figura seguente e la [Panoramica delle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagramma del funzionamento delle identità gestite per le risorse di Azure](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Authentication
Per eseguire qualsiasi operazione con Key Vault, è prima necessario eseguire l'autenticazione. Esistono tre modi per eseguire l'autenticazione a Key Vault:

- [Identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md): Quando si distribuisce un'app in una macchina virtuale in Azure, è possibile assegnare un'identità alla macchina virtuale che ha accesso a Key Vault. È anche possibile assegnare identità ad [altre risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md). Il vantaggio di questo approccio è che l'app o il servizio non gestisce la rotazione del primo segreto. Azure ruota automaticamente l'identità. Questo approccio è consigliato come procedura consigliata. 
- **Entità servizio e certificato**: È possibile usare un'entità servizio e un certificato associato con accesso a Key Vault. Questo approccio non è consigliato perché il proprietario o lo sviluppatore dell'applicazione deve ruotare il certificato.
- **Entità servizio e segreto**: Sebbene sia possibile usare un'entità servizio e un segreto per l'autenticazione a Key Vault, non è consigliabile. È difficile ruotare automaticamente il segreto di bootstrap usato per l'autenticazione Key Vault.


## <a name="key-vault-roles"></a>Ruoli dell'insieme di credenziali delle chiavi

La seguente tabella permette di capire meglio come l'insieme di credenziali chiave aiuti a soddisfare le esigenze degli sviluppatori e degli amministratori della sicurezza.

| Role | Presentazione del problema | Soluzione offerta dall'insieme di credenziali chiave di Azure |
| --- | --- | --- |
| Sviluppatore di un'applicazione Azure |"Desidero scrivere un'applicazione per Azure che usa chiavi per la firma e la crittografia. Tuttavia, desidero che queste chiavi siano esterne all'applicazione, in modo che la soluzione sia adatta a un'applicazione geograficamente distribuita. <br/><br/>Voglio che queste chiavi e questi segreti siano protetti, senza dover scrivere manualmente il codice, e voglio poterli usare facilmente dall'applicazione ottenendo prestazioni ottimali." |√ Le chiavi vengono archiviate in un insieme di credenziali e richiamate dall'URI quando è necessario.<br/><br/> √ Le chiavi vengono protette da Azure con algoritmi standard del settore, lunghezze delle chiavi e moduli di protezione hardware.<br/><br/> √ Le chiavi vengono elaborate in moduli di protezione hardware che risiedono negli stessi data center di Azure in cui si trovano le applicazioni. In questo modo si ottiene una migliore affidabilità e una latenza ridotta rispetto a chiavi che si trovano in una posizione diversa, ad esempio in locale. |
| Sviluppatore di software come un servizio (SaaS) |"Non voglio essere in alcun modo responsabile delle chiavi e dei segreti dei tenant dei miei clienti. <br/><br/>Voglio che siano i clienti a possedere e gestire le chiavi per potermi concentrare su ciò che so fare meglio, ovvero fornire le principali funzionalità del software." |√ I clienti possono importare le loro chiavi in Azure e gestirle. Quando un'applicazione SaaS deve eseguire operazioni di crittografia usando le chiavi dei clienti, Key Vault esegue queste operazioni per conto dell'applicazione. L'applicazione non vede le chiavi dei clienti. |
| Responsabile della sicurezza |"Voglio la certezza che le applicazioni siano conformi ai moduli di protezione hardware FIPS 140-2 livello 2 per la gestione delle chiavi protetta. <br/><br/>Voglio assicurarmi che la mia organizzazione abbia il controllo del ciclo di vita delle chiavi e possa monitorare l'utilizzo delle chiavi. <br/><br/>Anche se usiamo più servizi e risorse di Azure, voglio gestire le chiavi da una sola posizione in Azure. |√ I moduli di protezione hardware hanno la certificazione FIPS 140-2 livello 2.<br/><br/>√ L'insieme di credenziali delle chiavi è progettato in modo che Microsoft non possa vedere o estrarre le chiavi.<br/><br/>√ L'utilizzo delle chiavi viene registrato quasi in tempo reale.<br/><br/>√ L'insieme di credenziali offre un'unica interfaccia, indipendentemente dal numero di insiemi di credenziali disponibili in Azure, dalle aree supportate e dalle applicazioni che li usano. |

Chiunque abbia una sottoscrizione di Azure può creare e usare insiemi di credenziali delle chiavi. Sebbene Key Vault vantaggi per gli sviluppatori e gli amministratori della sicurezza, può essere implementato e gestito dall'amministratore di un'organizzazione che gestisce altri servizi di Azure. Questo amministratore può ad esempio accedere con una sottoscrizione di Azure, creare un insieme di credenziali per l'organizzazione in cui archiviare le chiavi e quindi essere responsabile delle attività operative come le seguenti:

- Creare o importare una chiave o un segreto
- Revocare o eliminare una chiave o un segreto
- Autorizzare gli utenti o le applicazioni per l'accesso all'insieme di credenziali delle chiavi, per consentire la gestione o l'uso delle chiavi e dei segreti corrispondenti
- Configurare l'utilizzo delle chiavi (ad esempio, la firma o la crittografia)
- Monitorare l'utilizzo delle chiavi

Questo amministratore offre agli sviluppatori gli URI da chiamare dalle applicazioni. Questo amministratore fornisce inoltre le informazioni di registrazione dell'utilizzo chiave all'amministratore della sicurezza. 

![Panoramica del funzionamento di Azure Key Vault][1]

Gli sviluppatori possono gestire le chiavi anche direttamente, usando le API. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure](key-vault-developers-guide.md).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [proteggere l'](key-vault-secure-your-key-vault.md)insieme di credenziali.

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
L'insieme di credenziali delle chiavi di Azure è disponibile nella maggior parte delle aree. Per altre informazioni, vedere la [pagina Insieme di credenziali delle chiavi - Prezzi](https://azure.microsoft.com/pricing/details/key-vault/).
