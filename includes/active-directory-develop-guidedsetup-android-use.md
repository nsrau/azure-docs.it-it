
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Usare MSAL per ottenere un token per l'API Microsoft Graph

1.  In **app** > **java** > **{dominio}.{nomeapp}** aprire `MainActivity`. 
2.  Aggiungere le importazioni seguenti:

    ```java
    import android.app.Activity;
    import android.content.Intent;
    import android.util.Log;
    import android.view.View;
    import android.widget.Button;
    import android.widget.TextView;
    import android.widget.Toast;
    import com.android.volley.*;
    import com.android.volley.toolbox.JsonObjectRequest;
    import com.android.volley.toolbox.Volley;
    import org.json.JSONObject;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;
    import com.microsoft.identity.client.*;
    ```

3. Sostituire la classe `MainActivity` con il codice seguente:

    ```java
    public class MainActivity extends AppCompatActivity {

        final static String CLIENT_ID = "[Enter the application Id here]";
        final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
        final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

        /* UI & Debugging Variables */
        private static final String TAG = MainActivity.class.getSimpleName();
        Button callGraphButton;
        Button signOutButton;

        /* Azure AD Variables */
        private PublicClientApplication sampleApp;
        private AuthenticationResult authResult;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            callGraphButton = (Button) findViewById(R.id.callGraph);
            signOutButton = (Button) findViewById(R.id.clearCache);

            callGraphButton.setOnClickListener(new View.OnClickListener() {
                public void onClick(View v) {
                    onCallGraphClicked();
                }
            });

            signOutButton.setOnClickListener(new View.OnClickListener() {
                public void onClick(View v) {
                    onSignOutClicked();
                }
            });

    /* Configure your sample app and save state for this activity */
            sampleApp = null;
            if (sampleApp == null) {
                sampleApp = new PublicClientApplication(
                        this.getApplicationContext(),
                        CLIENT_ID);
            }

    /* Attempt to get a user and acquireTokenSilent
    * If this fails we do an interactive request
    */
            List<User> users = null;

            try {
                users = sampleApp.getUsers();

                if (users != null && users.size() == 1) {
            /* We have 1 user */

                    sampleApp.acquireTokenSilentAsync(SCOPES, users.get(0), getAuthSilentCallback());
                } else {
            /* We have no user */

            /* Let's do an interactive request */
                    sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
                }
            } catch (MsalClientException e) {
                Log.d(TAG, "MSAL Exception Generated while getting users: " + e.toString());

            } catch (IndexOutOfBoundsException e) {
                Log.d(TAG, "User at this position does not exist: " + e.toString());
            }

        }

    //
    // App callbacks for MSAL
    // ======================
    // getActivity() - returns activity so we can acquireToken within a callback
    // getAuthSilentCallback() - callback defined to handle acquireTokenSilent() case
    // getAuthInteractiveCallback() - callback defined to handle acquireToken() case
    //

        public Activity getActivity() {
            return this;
        }

        /* Callback method for acquireTokenSilent calls 
        * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
        * else errors that we need to do an interactive request.
        */
        private AuthenticationCallback getAuthSilentCallback() {
            return new AuthenticationCallback() {
                @Override
                public void onSuccess(AuthenticationResult authenticationResult) {
                /* Successfully got a token, call Graph now */
                    Log.d(TAG, "Successfully authenticated");

                /* Store the authResult */
                    authResult = authenticationResult;

                /* call graph */
                    callGraphAPI();

                /* update the UI to post call Graph state */
                    updateSuccessUI();
                }

                @Override
                public void onError(MsalException exception) {
                /* Failed to acquireToken */
                    Log.d(TAG, "Authentication failed: " + exception.toString());

                    if (exception instanceof MsalClientException) {
                    /* Exception inside MSAL, more info inside MsalError.java */
                    } else if (exception instanceof MsalServiceException) {
                    /* Exception when communicating with the STS, likely config issue */
                    } else if (exception instanceof MsalUiRequiredException) {
                    /* Tokens expired or no session, retry with interactive */
                    }
                }

                @Override
                public void onCancel() {
                /* User cancelled the authentication */
                    Log.d(TAG, "User cancelled login.");
                }
            };
        }

        /* Callback used for interactive request.  If succeeds we use the access
            * token to call the Microsoft Graph. Does not check cache
            */
        private AuthenticationCallback getAuthInteractiveCallback() {
            return new AuthenticationCallback() {
                @Override
                public void onSuccess(AuthenticationResult authenticationResult) {
                /* Successfully got a token, call graph now */
                    Log.d(TAG, "Successfully authenticated");
                    Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

                /* Store the auth result */
                    authResult = authenticationResult;

                /* call Graph */
                    callGraphAPI();

                /* update the UI to post call Graph state */
                    updateSuccessUI();
                }

                @Override
                public void onError(MsalException exception) {
                /* Failed to acquireToken */
                    Log.d(TAG, "Authentication failed: " + exception.toString());

                    if (exception instanceof MsalClientException) {
                    /* Exception inside MSAL, more info inside MsalError.java */
                    } else if (exception instanceof MsalServiceException) {
                    /* Exception when communicating with the STS, likely config issue */
                    }
                }

                @Override
                public void onCancel() {
                /* User cancelled the authentication */
                    Log.d(TAG, "User cancelled login.");
                }
            };
        }

        /* Set the UI for successful token acquisition data */
        private void updateSuccessUI() {
            callGraphButton.setVisibility(View.INVISIBLE);
            signOutButton.setVisibility(View.VISIBLE);
            findViewById(R.id.welcome).setVisibility(View.VISIBLE);
            ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
                    authResult.getUser().getName());
            findViewById(R.id.graphData).setVisibility(View.VISIBLE);
        }

        /* Use MSAL to acquireToken for the end-user
        * Callback will call Graph api w/ access token & update UI
        */
        private void onCallGraphClicked() {
            sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
        }

        /* Handles the redirect from the System Browser */
        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            sampleApp.handleInteractiveRequestRedirect(requestCode, resultCode, data);
        }

    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Altre informazioni
#### <a name="get-a-user-token-interactively"></a>Ottenere un token utente in modo interattivo
Chiamando il metodo `AcquireTokenAsync` viene visualizzata una finestra in cui viene chiesto agli utenti di eseguire l'accesso. Le applicazioni in genere richiedono agli utenti di eseguire l'accesso in modo interattivo quando devono accedere per la prima volta a una risorsa protetta. Potrebbe essere necessario eseguire l'accesso anche quando un'operazione invisibile all'utente per l'acquisizione di un token ha esito negativo, ad esempio a causa della scadenza della password di un utente.

#### <a name="get-a-user-token-silently"></a>Ottenere un token utente in modo automatico
Il metodo `AcquireTokenSilentAsync` gestisce le acquisizioni e i rinnovi dei token senza alcuna interazione da parte dell'utente. Dopo la prima esecuzione di `AcquireTokenAsync`, per le chiamate successive il metodo generalmente usato per ottenere i token per accedere alle risorse protette è `AcquireTokenSilentAsync`, perché le chiamate per richiedere o rinnovare i token vengono effettuate in modo invisibile all'utente.

Il metodo `AcquireTokenSilentAsync` avrà infine esito negativo, ad esempio perché l'utente ha effettuato la disconnessione o ha modificato la propria password su un altro dispositivo. Se MSAL rileva che il problema può essere risolto richiedendo un'azione interattiva, viene attivata un'eccezione `MsalUiRequiredException`. L'applicazione può gestire questa eccezione in due modi:

* Può eseguire immediatamente una chiamata a `AcquireTokenAsync` e richiedere così all'utente di eseguire l'accesso. Questo modello viene usato in genere nelle applicazioni online in cui non è disponibile contenuto offline per l'utente. L'esempio generato in questa configurazione guidata segue questo modello, il cui funzionamento può essere osservato la prima volta che si esegue l'esempio. 
    * Dato che nessun utente ha usato l'applicazione, `PublicClientApp.Users.FirstOrDefault()` contiene un valore Null e viene generata un'eccezione `MsalUiRequiredException`. 
    * Il codice dell'esempio gestisce quindi l'eccezione chiamando `AcquireTokenAsync` e richiedendo così all'utente di eseguire l'eccesso. 

* Può presentare un'indicazione visiva per informare gli utenti che è necessario un accesso interattivo e consentire così di scegliere il momento opportuno per accedere. In alternativa, l'applicazione può riprovare a eseguire `AcquireTokenSilentAsync` in un secondo momento. Questo modello viene in genere adottato quando gli utenti possono usare altre funzionalità dell'applicazione senza interruzioni, ad esempio quando nell'applicazione è disponibile contenuto offline. In questo caso, gli utenti possono decidere se vogliono eseguire l'accesso per accedere alla risorsa protetta oppure aggiornare le informazioni obsolete. In alternativa, l'applicazione può decidere di riprovare a eseguire `AcquireTokenSilentAsync` quando la rete viene ripristinata dopo essere stata temporaneamente non disponibile. 
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Chiamare l'API Microsoft Graph usando il token appena ottenuto
Aggiungere i metodi seguenti alla classe `MainActivity`:

```java
/* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() throws AuthFailureError {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the Graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = (TextView) findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```
<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Altre informazioni sull'esecuzione di una chiamata REST a un'API protetta

In questa applicazione di esempio, `callGraphAPI` chiama `getAccessToken` e quindi effettua una richiesta HTTP `GET` a una risorsa che richiede un token e restituisce il contenuto. Questo metodo aggiunge il token acquisito nell'intestazione di autorizzazione HTTP. Per questo esempio, la risorsa è l'endpoint *me* dell'API Microsoft Graph, che visualizza le informazioni del profilo dell'utente.
<!--end-collapse-->

## <a name="set-up-sign-out"></a>Configurare la disconnessione

Aggiungere i metodi seguenti alla classe `MainActivity`:

```java
/* Clears a user's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 */
private void onSignOutClicked() {

    /* Attempt to get a user and remove their cookies from cache */
    List<User> users = null;

    try {
        users = sampleApp.getUsers();

        if (users == null) {
            /* We have no users */

        } else if (users.size() == 1) {
            /* We have 1 user */
            /* Remove from token cache */
            sampleApp.remove(users.get(0));
            updateSignedOutUI();

        }
        else {
            /* We have multiple users */
            for (int i = 0; i < users.size(); i++) {
                sampleApp.remove(users.get(i));
            }
        }

        Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
                .show();

    } catch (MsalClientException e) {
        Log.d(TAG, "MSAL Exception Generated while getting users: " + e.toString());

    } catch (IndexOutOfBoundsException e) {
        Log.d(TAG, "User at this position does not exist: " + e.toString());
    }
}

/* Set the UI for signed-out user */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");
}
```
<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>Altre informazioni sulla disconnessione degli utenti

Il metodo `onSignOutClicked` nel codice precedente rimuove gli utenti dalla cache utente di MSAL. In questo modo, MSAL dimentica l'utente corrente e una futura richiesta di acquisizione di un token avrà esito positivo solo se eseguita in modo interattivo.

Nonostante l'applicazione in questo esempio supporti singoli utenti, MSAL supporta anche scenari con accesso contemporaneo di più account, ad esempio un'applicazione di posta elettronica in cui un utente ha più account.
<!--end-collapse-->
