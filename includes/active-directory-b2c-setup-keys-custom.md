
### <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Aggiungere chiavi di firma e di crittografia al tenant B2C per l'uso da parte di criteri personalizzati

1. Passare al pannello Framework dell'esperienza di gestione delle identità nelle impostazioni del tenant Azure AD B2C.
1. Selezionare `Policy Keys` per visualizzare le chiavi disponibili nel tenant. Se `B2C_1A_TokenSigningKeyContainer` esiste, ignorare questa chiave.
1. Creare `TokenSigningKeyContainer`.  
 * Fare clic su `+Add`.
 * Opzioni > `Generate`.
 * Nome > `TokenSigningKeyContainer`. Il prefisso B2C_1A_ potrebbe essere aggiunto automaticamente.
 * Tipo chiave > `RSA`.
 * Date > Usare i valori predefiniti.
 * Uso chiave > `Signature`.
1. Fare clic su`Create`.
1. Se una chiave denominata `B2C_1A_TokenEncryptionKeyContainer` esiste già, ignorare questa chiave.
1. Creare `TokenEncryptionKeyContainer`.
 * Opzioni > `Generate`.
 * Nome > `TokenSigningKeyContainer`. Il prefisso B2C_1A_ potrebbe essere aggiunto automaticamente.
 * Tipo chiave > `RSA`.
 * Date > Usare i valori predefiniti.
 * Uso chiave > `Encryption`.
1. Fare clic su`Create`.


[!TIP]
I passaggi seguenti sono FACOLTATIVI se si vogliono offrire provider di identità basati su social network o provider di identità federati agli utenti finali.  Facebook offre un buon punto di partenza per ottenere informazioni sui provider di identità esterni con Azure AD B2C e l'uso di criteri personalizzati.

1. Creare `FacebookSecret`.  Anche se facoltativo, è consigliabile eseguire questo passaggio per testare facilmente la capacità di federazione esterna.  Questo permette di creare un solido punto di partenza per l'ulteriore sviluppo di criteri con altri provider di identità.
 * Fare clic su `+Add`.
 * Opzioni > `Manual`.
 * Nome > `FacebookSecret`. Il prefisso B2C_1A_ potrebbe essere aggiunto automaticamente.
 * Segreto > Immettere il segreto FacebookSecret disponibile in developers.facebook.com.  *Non si tratta dell'ID dell'app Facebook*.
 * Uso chiave > Firma.
1. Fare clic su `Create` e confermare la creazione. Prendere nota del nome.

[!NOTE]
Se si usano i criteri predefiniti di Azure AD B2C, in genere si usa lo stesso segreto per i criteri predefiniti e quelli personalizzati. 
