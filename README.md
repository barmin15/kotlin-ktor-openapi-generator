# Custom OpenAPI Generator for Kotlin Ktor

---

## **1. Download Default Templates Or use this template**

```bash
openapi-generator author template -g kotlin-server -o ./custom-template
```

## **2. Replace its api.mustache file contents with the following:**

```kotlin
{{>licenseInfo}}
package {{apiPackage}}

import io.ktor.http.*
import io.ktor.server.application.*
import io.ktor.server.auth.*
import io.ktor.server.response.*
import io.ktor.server.routing.*
{{#featureResources}}
import {{packageName}}.Paths
import io.ktor.server.resources.options
import io.ktor.server.resources.get
import io.ktor.server.resources.post
import io.ktor.server.resources.put
import io.ktor.server.resources.delete
import io.ktor.server.resources.head
import io.ktor.server.resources.patch
{{/featureResources}}
import {{packageName}}.infrastructure.ApiPrincipal
{{#imports}}import {{import}}
{{/imports}}

{{#operations}}
fun Route.{{classname}}(api: {{classname}}) {
{{#operation}}
{{#hasAuthMethods}}
{{#authMethods}}
authenticate("{{{name}}}") {
{{/authMethods}}
{{/hasAuthMethods}}

    /**
     * API Operation: {{summary}}
     * Method: {{httpMethod}} {{path}}
     * Description: {{description}}
     * Returns: {{#responses}}{{^isDefault}}{{dataType}}{{/isDefault}}{{/responses}}
     */
    {{#lambda.lowercase}}{{httpMethod}}{{/lambda.lowercase}}("{{path}}") {
        // Call API method
        api.{{operationId}}(call)
    }

    {{#hasAuthMethods}}
    }
    {{/hasAuthMethods}}

    {{/operation}}
}
{{/operations}}
```

## **3. Generate ktor API code:**

```bash
openapi-generator generate -i {yaml file path} -g kotlin-server -t {custom template path} -o {path for generation}

```