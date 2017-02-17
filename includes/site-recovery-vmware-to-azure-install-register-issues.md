
### <a name="installation-failures"></a>Errori di installazione
| **Messaggio di errore di esempio** | **Azione consigliata** |
|--------------------------|------------------------|
|ERRORE. Non è stato possibile caricare gli account. Errore: System.IO.IOException. Impossibile leggere dati dalla connessione del trasporto, durante l'installazione e la registrazione del server CS.| Verificare che nel computer sia abilitato TLS 1.0. |

### <a name="registration-failures"></a>Errori di registrazione
È possibile eseguire il debug degli errori di registrazione esaminando i log nella cartella **%ProgramData%\ASRLogs**.

| **Messaggio di errore di esempio** | **Azione consigliata** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type: SrsRestApiClientLib.AcsException,InnerException.<br>Message: ACS50008: SAML token is invalid.<br>Trace ID: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>Correlation ID: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>Timestamp: **2016-12-12 14:50:08Z<br>** | Verificare che l'ora del clock di sistema non differisca di oltre 15 rispetto all'ora locale. Eseguire di nuovo il programma di installazione per completare la registrazione.|
|**09:35:27**:DRRegistrationException while trying to get all disaster recovery vault for the selected certificate: : Threw Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception.Message: ACS50008: SAML token is invalid.<br>Trace ID: e5ad1af1-2d39-4970-8eef-096e325c9950<br>Correlation ID: abe9deb8-3e64-464d-8375-36db9816427a<br>Timestamp: **2016-05-19 01:35:39Z**<br> | Verificare che l'ora del clock di sistema non differisca di oltre 15 rispetto all'ora locale. Eseguire di nuovo il programma di installazione per completare la registrazione.|
|06:28:45:Failed to create certificate<br>06:28:45:Setup cannot proceed. A certificate required to authenticate to Site Recovery cannot be created. Rerun Setup | Verificare di eseguire il programma di installazione come amministratore locale. |


<!--HONumber=Feb17_HO2-->


