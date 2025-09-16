# Lista Tarefas Plus (Expo + React Native)

App de lista de tarefas com:
- Autenticação com Google e E-mail/Senha (Firebase Auth)
- Login persistente (auto-login)
- Firestore por usuário (CRUD + `onSnapshot` para tempo real)
- Tema claro/escuro com persistência (AsyncStorage)
- Internacionalização PT/EN com troca dinâmica (i18next)
- Notificações locais com agendamento (expo-notifications)
- TanStack Query consumindo API externa (frases motivacionais)

## Requisitos

- Node LTS e `npm` ou `yarn`
- Conta no Firebase (Web App)
- Expo CLI (`npm i -g expo-cli`) e EAS CLI (`npm i -g eas-cli`) para build

## Configuração

1) Crie um projeto Firebase e um App Web. Copie as credenciais (apiKey, projectId, etc).

2) Crie credenciais OAuth do Google:
   - Web (para `expoClientId`)
   - Android
   - iOS
   Copie os client IDs para o arquivo `app.json` em `expo.extra.google`.

3) Edite `app.json`:
```json
"extra": {
  "firebase": { "...": "..." },
  "google": {
    "expoClientId": "WEB_CLIENT_ID.apps.googleusercontent.com",
    "iosClientId": "IOS_CLIENT_ID.apps.googleusercontent.com",
    "androidClientId": "ANDROID_CLIENT_ID.apps.googleusercontent.com"
  }
}
```

4) Instale dependências:
```bash
npm install
```

5) Rode em desenvolvimento:
```bash
npx expo start
```

## Modelagem de Dados (Firestore)

Cada usuário possui sua coleção:
```
users/{uid}/tasks/{taskId}
```

Documento `Task`:
```js
{
  title: 'Estudar React Native',
  description: 'Focar no TanStack Query e Notificações',
  completed: false,
  dueDate: '2025-09-10T14:00:00Z', // ISO string (ou null)
  createdAt: Timestamp,
  updatedAt: Timestamp
}
```

## Notificações
- Permissões solicitadas no primeiro acesso.
- Agendadas por tarefa para `dueDate` futura e `completed = false`.
- Canceladas quando a tarefa é concluída/sem dueDate.
- Mapeamento `taskId -> notificationId` persistido em AsyncStorage (por dispositivo).

## Navegação
- SignIn (sem header)
- Tasks (lista + FAB)
- TaskForm (criar/editar)
- Settings (tema e idioma)

## Build (APK/AAB com EAS)
```bash
eas login
eas build -p android --profile production
eas build -p ios --profile production
```

## Observações
- Para Google Sign-In no Expo Managed, usamos `expo-auth-session` + `signInWithCredential` do Firebase.
- Para ordenação, as tarefas são renderizadas por `updatedAt` desc (via Firestore). Você pode ajustar facilmente para ordenar por `dueDate` no cliente.
- Se quiser que o agendamento se replique entre dispositivos, salve o `dueDate` no Firestore (já salvo) e re-agende localmente ao abrir o app (já feito via `ensureTaskNotification`).

## Estrutura
```
src/
  components/
  firebase/
  i18n/
  navigation/
  providers/
  screens/
  services/
  types/
  utils/
```

## Licença
MIT
