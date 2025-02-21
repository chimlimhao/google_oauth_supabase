# Next.js Google OAuth with Supabase

A reusable Next.js 14 component for implementing Google OAuth authentication using Supabase. This component provides both client-side and server-side authentication capabilities.

## Features

- 🔐 Google OAuth Authentication
- 🔄 Server-side and Client-side Authentication Support
- ⚡ Built with Next.js 14 App Router
- 🎨 Styled with Tailwind CSS
- 🎯 TypeScript Support
- 🛠️ Easy Integration
- 🔒 Secure Token Handling
- 🎭 Built-in Error Handling

## Prerequisites

Before you begin, ensure you have:

1. A Supabase account and project set up
2. Google Cloud Console project with OAuth 2.0 credentials
3. Node.js 18.17 or later installed

## Setup Instructions

### 1. Install Dependencies

```bash
npm install @supabase/auth-helpers-nextjs @supabase/ssr @supabase/supabase-js
```

### 2. Configure Supabase

1. Create a new project in Supabase
2. Get your project URL and anon key from Project Settings > API
3. Create a `.env.local` file in your project root:

```env
NEXT_PUBLIC_SUPABASE_URL=your_supabase_project_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
```

### 3. Configure Google OAuth

1. Go to [Google Cloud Console](https://console.cloud.google.com)
2. Create a new project or select an existing one
3. Enable the Google+ API
4. Configure OAuth consent screen
5. Create OAuth 2.0 credentials (Web application type)
6. Add authorized redirect URIs:
   - `https://your-supabase-project.supabase.co/auth/v1/callback`
   - `http://localhost:3000/auth/callback` (for development)
7. Add your Google client ID and secret in Supabase Dashboard:
   - Go to Authentication > Providers > Google
   - Enable Google provider
   - Add your Client ID and Client Secret

### 4. Component Integration

1. Copy the following files to your project:
   - `components/GoogleSignin.tsx` - Client component for Google sign-in button
   - `app/auth/callback/route.ts` - Server-side callback handler
   - `utils/supabase/client.ts` - Supabase client configuration
   - `utils/supabase/server.ts` - Server-side Supabase configuration

2. Add the authentication callback route:

```typescript
// app/auth/callback/route.ts
import { createClient } from '@/utils/supabase/server'
import { NextResponse } from 'next/server'
import { cookies } from 'next/headers'

export async function GET(request: Request) {
  const requestUrl = new URL(request.url)
  const code = requestUrl.searchParams.get('code')
  
  if (code) {
    const supabase = createClient()
    const { error } = await supabase.auth.exchangeCodeForSession(code)
    
    if (!error) {
      return NextResponse.redirect(requestUrl.origin)
    }
  }

  // Return the user to an error page with some instructions
  return NextResponse.redirect(`${requestUrl.origin}/auth-error`)
}
```

3. Use the GoogleSignin component in your page:

```typescript
import GoogleSignin from '@/components/GoogleSignin'

export default function LoginPage() {
  return (
    <div>
      <GoogleSignin />
    </div>
  )
}
```

## Usage Example

```typescript
// pages/protected.tsx
import { createClient } from '@/utils/supabase/server'
import { redirect } from 'next/navigation'

export default async function ProtectedPage() {
  const supabase = createClient()
  const { data: { session } } = await supabase.auth.getSession()

  if (!session) {
    redirect('/login')
  }

  return (
    <div>
      <h1>Protected Content</h1>
      <p>Welcome {session.user.email}</p>
    </div>
  )
}
```

## Error Handling

The component includes built-in error handling for common scenarios:
- Invalid credentials
- Network errors
- Session expiration
- Callback errors

Error messages are displayed to users in a user-friendly format using toast notifications.

## Customization

### Styling

The component uses Tailwind CSS for styling. You can customize the appearance by:
1. Modifying the Tailwind classes in `GoogleSignin.tsx`
2. Adjusting the theme in your `tailwind.config.js`

### Redirect URLs

You can customize the redirect behavior after authentication:

```typescript
<GoogleSignin redirectTo="/dashboard" />
```

## Security Considerations

1. Always use environment variables for sensitive data
2. Implement proper CORS policies
3. Use HTTP-only cookies for session management
4. Regularly rotate your Supabase keys
5. Keep your dependencies updated

## Troubleshooting

Common issues and solutions:

1. **Callback URL Mismatch**: Ensure your callback URLs in Google Console match your Supabase configuration
2. **CORS Errors**: Check your allowed origins in Supabase
3. **Session Issues**: Clear browser cookies and try again
4. **Type Errors**: Ensure all required types are imported

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

MIT License - feel free to use this component in your projects.
