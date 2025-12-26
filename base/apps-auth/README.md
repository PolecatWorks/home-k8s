# Initial SA for Keycloak

When you first create the keycloak instance you need to manually log in and create a Service Account for Flux to use in subsequent management.
You can get the password with this command

    kubectl -n auth get secrets/keycloak-keycloakx-admin-creds -o jsonpath="{.data.password}" | base64 -d

Once you have that password you can log into Keycloak using default username: admin

Once there create a new Service Account in the Master Realm called: master-admin-sa
Enable: Client authentication and Authorization

You should now be able to navigate to the Client definition and open the Credentials section and copy out the Client Secret

Then create the k8s secret with this content using this command:

    kubectl -n auth create secret generic keycloak-master-sa --from-literal=username=master-admin-sa --from-literal=password="KEYCLOAK PASSWORD HERE"

From here your Keycloak object should now be able to connect. Check with this command:

    kubectl -n auth get keycloak

This will give you conenction but likely nothing else. You now need to upgrade the capability of the Service Account. Open the "Service Account Role" and "Assign role" then choose "Realm roles" and then select "admin".
At this point you should be able to confirm by checking the status of the KeycloakRealms/master object.
