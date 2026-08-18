[README.md](https://github.com/user-attachments/files/31160288/README.md)
# CRM Social — Gerardo

App PWA para tu CRM de capital social. HTML + Firebase Firestore.

- **39 contactos** precargados desde el CSV
- Panel con contador de activos + gráfica de pastel por categoría
- Búsqueda por nombre/apellido
- Perfil editable con todos los campos que pediste
- Notas ilimitadas (añadir / ver / eliminar)
- Funciona offline en modo demo (localStorage) mientras conectas Firebase
- Sincronización en tiempo real cuando Firebase está configurado

---

## Paso 1 · Subir a GitHub

Puedes reutilizar tu repo existente `inmbatfapin.github.io` (mismo dominio donde vive rentas-app) o crear uno nuevo. **Recomendado: mismo repo, subcarpeta nueva** para que compartas dominio.

### Opción A — Repo dedicado (más simple)

1. Ve a https://github.com/new
2. Nombre: `crm-social` (o el que quieras)
3. **Público**, marca "Add a README"
4. En el repo nuevo → **Add file → Upload files**
5. Sube `index.html` (y opcionalmente `manifest.json` si quieres PWA)
6. Commit
7. **Settings → Pages** → Source: `Deploy from a branch` → Branch: `main` / `(root)` → Save
8. En 1-2 minutos estará en `https://inmbatfapin.github.io/crm-social/`

### Opción B — Subcarpeta en tu repo actual

1. Ve a tu repo `inmbatfapin/inmbatfapin.github.io`
2. **Add file → Create new file**
3. En el nombre escribe: `crm-social/index.html` (la barra crea la carpeta)
4. Pega el contenido de `index.html`
5. Commit
6. Queda en `https://inmbatfapin.github.io/crm-social/`

---

## Paso 2 · Configurar Firebase (para que se guarde en la nube)

Mientras no lo configures, la app funciona con `localStorage` (solo en ese navegador). Con Firebase, tendrás los datos desde cualquier dispositivo.

### 2.1 Reutilizar tu proyecto existente `rentas-app-a5d28`

Puedes usar el mismo proyecto Firebase de rentas-app. La colección `crm_social_contactos` no interfiere con nada.

1. Ve a https://console.firebase.google.com/
2. Entra a `rentas-app-a5d28`
3. **⚙ Configuración del proyecto** (arriba a la izquierda, ícono de engrane)
4. Baja hasta **Tus apps**. Si ya tienes una app web, haz clic en `</>`; si no, **Añadir app → Web**
5. Registra nombre: "CRM Social" (sin hosting)
6. Copia el bloque `firebaseConfig` que aparece — se ve así:
   ```js
   const firebaseConfig = {
     apiKey: "AIzaSy...",
     authDomain: "rentas-app-a5d28.firebaseapp.com",
     projectId: "rentas-app-a5d28",
     storageBucket: "rentas-app-a5d28.appspot.com",
     messagingSenderId: "123456...",
     appId: "1:123..."
   };
   ```

### 2.2 Habilitar Firestore

1. En el menú lateral: **Build → Firestore Database**
2. Si aún no lo has creado: **Create database** → modo **Producción** → región `nam5 (us-central)`
3. Ve a **Rules** y pega estas reglas mínimas (permiten leer/escribir solo a la colección del CRM):
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /crm_social_contactos/{doc} {
         allow read, write: if true;
       }
       // deja tus reglas de rentas-app abajo intactas
     }
   }
   ```
   > ⚠️ `allow read, write: if true` es abierto. Si te preocupa la privacidad, después conviene añadir Firebase Auth y restringir por tu UID. Para empezar, es lo más rápido.
4. **Publish**

### 2.3 Pegar tu config en el HTML

Abre `index.html`, busca este bloque (línea ~230 aprox):
```js
const firebaseConfig = {
  apiKey: "TU_API_KEY",
  authDomain: "TU_PROYECTO.firebaseapp.com",
  ...
};
```
Reemplázalo con el que copiaste. Guarda, commit y push a GitHub.

### 2.4 Autorizar el dominio de GitHub Pages

1. Firebase Console → **Build → Authentication → Settings → Authorized domains**
2. Añade: `inmbatfapin.github.io`

(Aunque no uses Auth ahora, esto evita problemas si más adelante lo agregas.)

---

## Paso 3 · Probar

1. Abre la URL de GitHub Pages en tu celular o compu
2. Si ves **"☁ Sincronizado con Firebase"** arriba a la izquierda, todo bien
3. La primera vez que carga con Firebase configurado, siembra los 39 contactos automáticamente. Solo pasa una vez.
4. Cualquier edición se guarda al toque en Firestore

---

## Estructura de datos en Firestore

**Colección:** `crm_social_contactos`
**Documento por contacto** (ID: `c001`, `c002`, …):
```json
{
  "id": "c001",
  "nombre": "Edmi Jurado",
  "categoria": "semov",
  "estatus": "amigo",
  "activo": true,
  "fuerza": "9",
  "apasiona": "...",
  "hobbies": "...",
  "familia": "...",
  "valora": "...",
  "logros": "...",
  "ofrezco": "...",
  "ayuda": "...",
  "proxima_accion": "...",
  "ultima_actualizacion": "2026-08-17",
  "notas": [
    { "id": "n001", "fecha": "2026-08-15", "texto": "Relación clave..." }
  ]
}
```

Categorías válidas: `cide`, `tepeyac`, `semov`, `ciecas`, `inmobiliaria`, `personal`, `otro`.

---

## Ideas para después (fáciles de añadir)

- Botón **"+ Nuevo contacto"** (ahora solo edita existentes)
- Recordatorios automáticos por próxima acción vencida
- Vista timeline de todas las notas de todos los contactos
- Exportar a CSV para respaldo
- Integrar con la app de rentas-app como pestaña
