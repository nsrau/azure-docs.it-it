<properties
   pageTitle="Guida per gli sviluppatori dell’insieme di credenziali chiave | Microsoft Azure"
   description="Gli sviluppatori possono utilizzare l'insieme di credenziali chiave di Azure per gestire le chiavi di crittografia all'interno dell'ambiente Microsoft Azure."
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/11/2015"
   ms.author="mbaldwin" />

# Guida per gli sviluppatori dell’insieme di credenziali chiave Azure

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

Gli sviluppatori possono utilizzare l'insieme di credenziali chiave di Azure per gestire le chiavi di crittografia all'interno dell'ambiente Microsoft Azure. L’insieme di credenziali chiave supporta più tipi di chiave e di algoritmi e può essere utilizzato con i moduli di protezione hardware (HSM) per le chiavi clienti di alto valore. Inoltre, è possibile utilizzare l’insieme di credenziali chiave per l'archiviazione protetta delle informazioni riservate che sono oggetti ottetto di dimensioni limitate senza una semantica specifica. Un insieme di credenziali chiave può contenere una combinazione di chiavi e di informazioni riservate. Il controllo di accesso per i tipi di oggetti viene gestito in modo indipendente.

Gli utenti, per i quali l'autorizzazione ha esito positivo, possono effettuare le seguenti operazioni:

- Gestire le chiavi di crittografia utilizzando [Crea](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Importa](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Aggiorna](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Elimina](https://msdn.microsoft.com/library/azure/dn903611.aspx) e altre operazioni

- Gestire le informazioni riservate utilizzando [Ottieni](https://msdn.microsoft.com/library/azure/dn903633.aspx), [aggiornamento] (https://msdn.microsoft.com/library/azure/dn986818.aspx, [Elimina](https://msdn.microsoft.com/library/azure/dn903613.aspx) e altre operazioni

- Utilizzare le chiavi di crittografia con le operazioni [Accedi](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verifica](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) e [Crittografa](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Decrittografa](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Le operazioni sugli insiemi di credenziali chiave vengono autenticate e autorizzate tramite Azure Active Directory.

## Programmazione dell'insieme di credenziali chiave

Il sistema di gestione delle credenziali chiave per i programmatori è costituito da diverse interfacce, che hanno come base REST.

### REST

L'API REST è alla base di tutte le interazioni a livello di programmazione con l'insieme di credenziali chiave.

L’insieme di credenziali chiave dispone di un proprio endpoint REST descritto nel [Riferimento all'API REST dell’insieme di credenziali chiave](https://msdn.microsoft.com/library/azure/dn903609.aspx)

### .NET

L'API .NET è un set di wrapper che consente l'implementazione tramite il modello di programmazione C# senza la necessità di interagire direttamente con l'endpoint REST. Qui è possibile trovare il [Riferimento all’API client .NET dell’insieme di credenziali chiave di Azure](https://msdn.microsoft.com/library/azure/dn903301.aspx).

### Node.js

L'API Node.js è un set di wrapper che consente l'implementazione tramite il modello di programmazione JavaScript senza la necessità di interagire direttamente con l'endpoint REST. Qui è possibile trovare [SDK di Microsoft Azure per la gestione dell’insieme di credenziali chiave Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest/).

## Gestione dell’insieme di credenziali chiave con PowerShell e l’interfaccia della riga di comando

Gli insiemi di credenziali chiave e le informazioni riservate possono inoltre essere gestiti tramite PowerShell e l’interfaccia della riga di comando, come descritto nei seguenti articoli:

- [Creare e gestire insiemi di credenziali chiave con PowerShell](key-vault-get-started.md)
- [Creare e gestire insiemi di credenziali chiave con l’interfaccia della riga di comando](key-vault-manage-with-cli.md)
- [Come generare e trasferire chiavi protette HSM per l’insieme di credenziali delle chiavi di Azure](https://msdn.microsoft.com/library/azure/dn903624.aspx)
- [Informazioni su chiavi e informazioni riservate](https://msdn.microsoft.com/library/azure/dn903623.aspx)

## Vedere anche

- [Esempi di codice di insieme di credenziali chiave di Azure](http://www.microsoft.com/download/details.aspx?id=45343)

<!---HONumber=August15_HO8-->