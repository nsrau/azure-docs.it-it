
* [Registrare un ID applicazione per l'app](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingProfiles/MaintainingProfiles.html#//apple_ref/doc/uid/TP40012582-CH30-SW991). Creare un ID app esplicito (non un ID app con carattere jolly) e per **ID pacchetto** utilizzare l'esatto **ID pacchetto** contenuto nel progetto di Guida introduttiva Xcode. È inoltre fondamentale verificare l'opzione relativa alle **notifiche push**. 

* Successivamente [abilitare le notifiche push per l'app](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW6). È possibile creare un certificato SSL di "Sviluppo" o "Distribuzione" (ricordarsi di selezionare l'opzione corrispondente nel portale di Azure in un secondo momento).

* Quindi [verificare che l'ID app consenta le notifiche push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW8).

* Infine, [aggiornare i profili di provisioning nel progetto di Guida introduttiva Xcode](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW10) e [verificare che il profilo di provisioning sia stato creato o rigenerato per abilitare le notifiche push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW12).

<!---HONumber=Oct15_HO3-->