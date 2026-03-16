# Build & Run
npm run build && npx cap sync android && cd android && .\gradlew assembleDebug && cd ..

# Validation
npx vitest run
npx tsc --noEmit

# Integration tests (must be run separately):
npx vitest run src/__tests__/integration