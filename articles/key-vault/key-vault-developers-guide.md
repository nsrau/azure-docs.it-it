<properties
   pageTitle="Guida per gli sviluppatori dell’insieme di credenziali chiave | Microsoft Azure"
   description="Gli sviluppatori possono utilizzare l'insieme di credenziali chiave di Azure per gestire le chiavi di crittografia all'interno dell'ambiente Microsoft Azure."
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/19/2016"
   ms.author="bruceper" />

# Guida per gli sviluppatori dell’insieme di credenziali chiave Azure

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

Gli sviluppatori possono usare l'insieme di credenziali delle chiavi di Azure per gestire le chiavi di crittografia all'interno dell'ambiente Microsoft Azure. L'insieme di credenziali delle chiavi supporta più tipi di chiavi e algoritmi e può essere usato con i moduli di protezione hardware per le chiavi di alto valore. Inoltre, è possibile utilizzare l’insieme di credenziali chiave per l'archiviazione protetta delle informazioni riservate che sono oggetti ottetto di dimensioni limitate senza una semantica specifica. Il controllo di accesso per i tipi di oggetti viene gestito in modo indipendente.

Gli utenti per i quali l'autorizzazione ha esito positivo possono eseguire le operazioni seguenti:

- Gestire le chiavi di crittografia utilizzando [Crea](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Importa](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Aggiorna](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Elimina](https://msdn.microsoft.com/library/azure/dn903611.aspx) e altre operazioni

- Gestire le chiavi private usando operazioni quali [Get](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Update](https://msdn.microsoft.com/library/azure/dn986818.aspx) e [Delete](https://msdn.microsoft.com/library/azure/dn903613.aspx)

- Utilizzare le chiavi di crittografia con le operazioni [Accedi](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verifica](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) e [Crittografa](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Decrittografa](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Le operazioni sugli insiemi di credenziali delle chiavi vengono autenticate e autorizzate tramite Azure Active Directory.

## Programmazione dell'insieme di credenziali chiave

Il sistema di gestione delle credenziali chiave per i programmatori è costituito da diverse interfacce, che hanno come base REST. [Informazioni di riferimento sull'API REST dell'insieme di credenziali delle chiavi](https://msdn.microsoft.com/library/azure/dn903609.aspx)

|[![.NET](./media/key-vault-developers-guide/net.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Documentazione su .NET SDK](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Documentazione su Node.js SDK](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[Pacchetto di .NET SDK](https://azure.microsoft.com/it-IT/documentation/api/)|[Pacchetto Node.js SDK](https://www.npmjs.com/package/azure-keyvault)|

## Gestione delle chiavi degli archivi

I contenitori dell’insieme di credenziali delle chiavi di Azure (insiemi di credenziali) possono essere gestiti tramite REST, PowerShell o l’interfaccia della riga di comando, come descritto nei seguenti articoli:

- [Creare e gestire insiemi di credenziali chiave con REST](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Creare e gestire insiemi di credenziali chiave con PowerShell](key-vault-get-started.md)
- [Creare e gestire insiemi di credenziali chiave con l’interfaccia della riga di comando](key-vault-manage-with-cli.md)


## Procedure

Gli articoli seguenti forniscono indicazioni specifiche dell’attività:

- [Come generare e trasferire chiavi protette HSM per l’insieme di credenziali delle chiavi di Azure](key-vault-hsm-protected-keys.md)

## esempi

- Questo download contiene sia l'applicazione di esempio HelloKeyVault che un esempio di servizio Web di Azure. [Esempi di codice di insieme di credenziali chiave di Azure](http://www.microsoft.com/download/details.aspx?id=45343)
- In questa esercitazione si apprenderà a usare l'insieme di credenziali chiave di Azure da un'applicazione Web in Azure. [Usare l'insieme di credenziali chiave di Azure da un'applicazione Web](key-vault-use-from-web-application.md)

## Supporto di librerie

- La [libreria principale dell'insieme di credenziali delle chiavi di Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0) fornisce le interfacce IKey e IKeyResolver per l'individuazione delle chiavi dagli identificatori e l'esecuzione di operazioni con le chiavi.

- Le [estensioni dell'insieme di credenziali delle chiavi di Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0) fornisce funzionalità estese per l'insieme di credenziali delle chiavi di Azure.

<!---HONumber=AcomDC_0211_2016-->