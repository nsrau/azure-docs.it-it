<properties 
	pageTitle="Creare entità ContentKey mediante .NET" 
	description="Informazioni su come creare chiavi simmetriche che forniscono l'accesso sicuro agli asset." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


# Creare entità ContentKey mediante .NET

Questo articolo fa parte della serie [Flusso di lavoro Video on Demand di Servizi multimediali](media-services-video-on-demand-workflow.md) e il [flusso di lavoro di streaming live di Servizi multimediali](media-services-live-streaming-workflow.md) .  

Servizi multimediali consente di creare nuovi asset crittografati e distribuirli. Un'entità **ContentKey** consente l'accesso sicuro alle entità **Asset**. 

Quando si crea un nuovo asset (ad esempio, prima di [caricare i file](media-services-dotnet-upload-files.md)), è possibile specificare le opzioni di crittografia seguenti: **StorageEncrypted**, **CommonEncryptionProtected** o **EnvelopeEncryptionProtected**. 

Quando si distribuiscono asset ai client, è possibile [configurarli per la crittografia dinamica](media-services-dotnet-configure-asset-delivery-policy.md) con una delle due seguenti opzioni: **DynamicEnvelopeEncryption** o **DynamicCommonEncryption**.

Gli asset crittografati devono essere associati alle entità **ContentKey**. Questo articolo descrive come creare una chiave simmetrica.

>[AZURE.NOTE] Quando si crea un nuovo asset **StorageEncrypted** mediante l'SDK di Servizi multimediali per .NET, l'entità **ContentKey** viene creata automaticamente e collegata all'asset.

## ContentKeyType

Uno dei valori che è necessario impostare quando si crea una chiave simmetrica è quello relativo al tipo. È possibile scegliere uno dei seguenti valori. 

    /// <summary>
    /// Specifies the type of a content key.
    /// </summary>
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for encrypting encoding configuration data that may contain sensitive preset information. 
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for url encryption.  Only used internally.
        /// </summary>
        UrlEncryption = 3,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }

## <a id="envelope_contentkey"></a>Creare un'entità ContentKey di tipo envelope

Il frammento di codice seguente crea una chiave simmetrica con tipo di crittografia envelope. Associa quindi la chiave all'asset specificato.

    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

chiamare

	IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



## <a id="common_contentkey"></a>Creare un'entità ContentKey di tipo common    

Il frammento di codice seguente crea una chiave simmetrica con tipo di crittografia common. Associa quindi la chiave all'asset specificato.

    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
chiamare

	IContentKey key = CreateCommonTypeContentKey(encryptedsset);
<!--HONumber=47-->
