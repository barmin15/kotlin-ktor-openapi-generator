# Custom OpenAPI Generator for Kotlin Ktor

---

## **Test usage with edge cases**

```bash
openapi-generator-cli generate \
    -i ./openapi/edge-cases.yml \
    -g kotlin-server \
    -t ./custom-template \
    -o ./generated \
    --additional-properties=useOneOfInterfaces=true,addOneOfInterfaceImports=true
```

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
     {{#queryParams}}
        // val {{paramName}} = call.request.queryParameters["{{paramName}}"]
        {{/queryParams}}
        {{#bodyParam}}
        // val requestBody = call.receive<{{dataType}}>()
        {{/bodyParam}}
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

## **3. Replace its model.mustache file contents with the following:**

```kotlin
{{>licenseInfo}}
package {{modelPackage}}

{{#imports}}import {{import}}
{{/imports}}

{{#models}}
{{#model}}
{{#isEnum}}
{{>enum_class}}
{{/isEnum}}
{{^isEnum}}
{{#oneOf}}

/**
 * Sealed interface for `oneOf` models in Kotlin
 */
import kotlinx.serialization.*
        import kotlinx.serialization.json.Json

        @Serializable
        sealed interface {{classname}} {
    val type: String

    companion object {
    fun fromJson(json: String): {{classname}} {
        return Json.decodeFromString(serializer(), json)
    }
}
}

{{#oneOf}}
{{#isPrimitiveType}}
@Serializable
@SerialName("Primitive{{datatype}}")
data class Primitive{{datatype}}(
        override val type: String = "{{datatype}}",
val value: {{datatype}}
) : {{classname}}
{{/isPrimitiveType}}

{{^isPrimitiveType}}
@Serializable
@SerialName("{{.}}")
data class {{.}}(
        override val type: String = "{{.}}"
) : {{classname}}
{{/isPrimitiveType}}
{{/oneOf}}

{{/oneOf}}
{{^oneOf}}
{{>data_class}}
{{/oneOf}}
{{/isEnum}}
{{/model}}
{{/models}}
```

## **4. Generate ktor API code:**

```bash
openapi-generator-cli generate \
    -i {yaml file path} \
    -g kotlin-server \
    -t {custom template path} \
    -o {path for generation} \
    --additional-properties=useOneOfInterfaces=true,addOneOfInterfaceImports=true
```