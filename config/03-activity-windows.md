# Semana 1

## Configuración inicial de AWS

Al terminar esta guía tendrás un usuario de trabajo, la AWS CLI instalada y tu terminal conectada a tu cuenta.

### Antes de empezar

- Una cuenta de AWS activa.
- MFA activado en tu cuenta.
- Saber cuál es la región de tu proyecto. La ves en la dirección de la consola: `...us-east-2.console.aws.amazon.com...` significa `us-east-2`.
- Windows 10 o 11 y acceso a PowerShell.

### 1. Crear tu usuario de trabajo y sus llaves (en la consola)

1. Escribe **IAM** en la barra de búsqueda y entra al servicio.
2. Menú izquierdo: **Usuarios** → **Crear usuario**.
3. Nombre: por ejemplo `abraham-admin`. Siguiente.
4. Elige **Adjuntar políticas directamente**, busca `AdministratorAccess` y **márcala con el cuadro de selección**. Luego crea el usuario.
5. Entra al usuario recién creado → pestaña **Credenciales de seguridad** → **Crear clave de acceso**.
6. Caso de uso: **Interfaz de línea de comandos (CLI)**. Acepta el aviso y crea.
7. Descarga el archivo `.csv`. La clave secreta solo se muestra esta vez.

> Verifica en la pestaña **Permisos** del usuario que aparezca `AdministratorAccess`. Si no quedó marcada, el usuario no podrá hacer nada.

### 2. Instalar la AWS CLI v2 en Windows

Primero abre **PowerShell** y revisa si ya la tienes:

```powershell
aws --version
```

Si responde `aws-cli/2.x.x`, salta al paso 3. Si el comando no se reconoce, instala la CLI.

1. Abre la guía oficial de instalación para Windows:
   https://docs.aws.amazon.com/es_es/cli/latest/userguide/getting-started-install.html
2. Descarga el instalador MSI de AWS CLI v2 para Windows.
3. Ejecuta el archivo `.msi` y sigue el asistente de instalación.
4. Cierra y vuelve a abrir PowerShell.

Comprueba la instalación:

```powershell
aws --version
```

Debe mostrar una versión `aws-cli/2.x.x`.

Para comprobar dónde quedó instalado:

```powershell
(Get-Command aws).Source
```

> No instales AWS CLI desde un paquete de terceros. Usa el instalador oficial de AWS.

### 3. Configurar la AWS CLI

En PowerShell, ejecuta:

```powershell
aws configure
```

Pide cuatro datos:

| Dato | Qué escribir |
|---|---|
| AWS Access Key ID | El ID de tu llave, está en el `.csv` |
| AWS Secret Access Key | Tu clave secreta, está en el `.csv` |
| Default region name | La región de tu proyecto, por ejemplo `us-east-2` |
| Default output format | `json` |

Cuando escribas la clave secreta, la terminal no mostrará los caracteres. Es normal.

### 4. Comprobar la conexión

```powershell
aws sts get-caller-identity
```

Debe responder algo así:

```json
{
    "UserId": "AIDAXXXXXXXXXXXXXXXXX",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/abraham-admin"
}
```

El `Arn` debe terminar en `user/` seguido del nombre de tu usuario. Este comando es el "¿quién soy?" de AWS: córrelo siempre antes de crear algo.

### 5. Comprobar los permisos

```powershell
aws iam list-users
```

Si muestra la lista de usuarios, tu usuario tiene permisos. Si responde `AccessDenied`, vuelve al paso 1 y revisa la política.

### 6. Ver o cambiar la configuración

```powershell
# Ver toda la configuración actual
aws configure list

# Ver la región
aws configure get region

# Cambiar la región
aws configure set region us-east-2
```

### Dónde queda guardado en Windows

| Archivo | Qué contiene |
|---|---|
| `%USERPROFILE%\.aws\credentials` | Tus llaves de acceso |
| `%USERPROFILE%\.aws\config` | La región y el formato de salida |

Desde PowerShell, puedes listar la carpeta con:

```powershell
Get-ChildItem "$HOME\.aws"
```

Están fuera de la carpeta del proyecto, así que no se suben a GitHub por accidente.

### Errores frecuentes

| Error | Causa | Solución |
|---|---|---|
| `aws no se reconoce como nombre de un cmdlet` | La CLI no está instalada o PowerShell no ha actualizado el PATH | Repetir el paso 2 y abrir una terminal nueva |
| `InvalidClientTokenId` o `SignatureDoesNotMatch` | Llave mal copiada | Repetir `aws configure` |
| `AccessDenied ... no identity-based policy allows` | Al usuario le falta la política | Adjuntar `AdministratorAccess` |
| `NoRegion` | No se configuró región | `aws configure set region <región>` |
| "No veo mis recursos" | La CLI y la consola están en regiones distintas | Usar la misma región en ambas |

### Reglas de seguridad

- Nunca subas el `.csv` ni tus llaves a GitHub, ni las pegues en un chat.
- No guardes el `.csv` dentro de la carpeta del proyecto.
- Si una llave se expone, desactívala o elimínala de inmediato en IAM y crea otra.
- No compartas tu pantalla mientras muestras el contenido de `credentials`.
