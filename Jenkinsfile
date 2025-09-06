pipeline {
  agent any

  environment {
    FIREBASE_TOKEN = credentials('firebase-token')
    PATH = "/usr/local/bin:/usr/bin:/bin:${PATH}"
  }

  options {
    timestamps()
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build') {
      steps {
        sh '''
          node -v
          npm -v
          firebase --version
          # If your app has a build step, uncomment:
          # npm ci
          # npm run build
        '''
      }
    }

    stage('Test') {
      steps {
        sh '''
          echo "Running tests..."
          # npm test || true   # keep pipeline moving if no tests yet
        '''
      }
    }

    stage('Staging') {
      steps {
        sh '''
          echo "Deploying to STAGING..."
          firebase deploy -P staging --only hosting --token "$FIREBASE_TOKEN"
        '''
      }
    }

    stage('Production') {
      when {
        branch 'main'
      }
      input {
        message "Deploy to PRODUCTION?"
        ok "Deploy"
      }
      steps {
        sh '''
          echo "Deploying to PRODUCTION..."
          firebase deploy -P production --only hosting --token "$FIREBASE_TOKEN"
        '''
      }
    }
  }

  post {
    always {
      echo "Pipeline finished (success or fail)."
    }
    failure {
      echo "Check Console Output and fix errors."
    }
  }
}
