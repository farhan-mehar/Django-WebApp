pipeline {
    agent any

    environment {
        PYTHON = "/usr/bin/python3"
        VENV = "venv"
        DJANGO_SETTINGS_MODULE = "django_web_app.settings"
        APP_NAME = "django_web_app"
        DB_NAME = "hrm_db"
        DB_USER = "hrm_user"
        DB_PASSWORD = "your_password_here"
        DB_HOST = "localhost"
        DB_PORT = "3306"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/farhan-mehar/Django-WebApp.git'
            }
        }

        stage('Setup Virtualenv') {
            steps {
                sh '''
                    if [ ! -d "$VENV" ]; then
                        $PYTHON -m venv $VENV
                    fi
                    source $VENV/bin/activate
                    pip install --upgrade pip
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    source $VENV/bin/activate
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Migrate Database') {
            steps {
                sh '''
                    source $VENV/bin/activate
                    python manage.py migrate --settings=$DJANGO_SETTINGS_MODULE
                '''
            }
        }

        stage('Collect Static Files') {
            steps {
                sh '''
                    source $VENV/bin/activate
                    python manage.py collectstatic --noinput --settings=$DJANGO_SETTINGS_MODULE
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    source $VENV/bin/activate
                    python manage.py test --settings=$DJANGO_SETTINGS_MODULE
                '''
            }
        }

        stage('Restart Application') {
            steps {
                sh '''
                    sudo systemctl restart gunicorn
                    sudo systemctl restart nginx
                '''
            }
        }
    }
}
