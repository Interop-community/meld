# eConsent (LEAP CDS) Functionality

## Registering eConsent (LEAP CDS) Functionality


Requirements:

1. CDS Endpoint

To access the LEAP CDS functionality available in the Meld Sandbox, first you will need to select the sandbox you would like to connect it to.

![](RackMultipart20220308-4-182hplt_html_abcdc386338a73e5.png)

Next you will need to navigate to the CDS Hooks Section of the sandbox you selected.

![](RackMultipart20220308-4-182hplt_html_9929eb98acc5d8a.png)

To access the LEAP CDS service, you will need to register a new service using the correct link.

[To be changed to production when available]

[https://dev-leap.interop.community/cds-services](https://dev-leap.interop.community/cds-serviceshttps:/dev-leap.interop.community/cds-services)

![](RackMultipart20220308-4-182hplt_html_9d4700e55dab1b58.png)

You&#39;ll now see the LEAP CDS service available for use in your sandbox.

![](RackMultipart20220308-4-182hplt_html_256f74269aa32cc9.png)

# Loading Persona Data Into Your Sandbox

Requirements

1. FHIR API endpoint URL
2. Postman
3. Test data is attached below:

![](RackMultipart20220308-4-182hplt_html_789cd2dad09ef582.png)

Loading data to an open/unsecured endpoint in Meld sandbox is straightforward:

Locate the open FHIR API endpoint and copy the link

![](RackMultipart20220308-4-182hplt_html_8d683f97412c67ee.png)

Paste the URL, select method, and add the content of the attached file in the body

![](RackMultipart20220308-4-182hplt_html_4fbb05c20ddc4e75.png)

_(Optional)_ For loading to a secured endpoint, a step to retrieve an authorization token is required.

Additional requirements for token retrieval:

1. Url for token: [https://qa-kc.interop.community/auth/realms/iol/protocol/openid-connect/token](https://qa-kc.interop.community/auth/realms/iol/protocol/openid-connect/token)
2. Client\_secret of authorizer on keycloak: 593ada9c-b1dc-444f-a2e6-30cc4add8146
3. Client\_id of authorizer on keycloak: reference-auth

![](RackMultipart20220308-4-182hplt_html_ab5111b9d856d55d.png)

Once received a token, add Authorization in the header

![](RackMultipart20220308-4-182hplt_html_61183506b5b60390.png)