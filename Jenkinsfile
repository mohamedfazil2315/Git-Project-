pipeline {
    agent any

    environment {
        APP_DIR    = "/home/ubuntu/flask-app"
        VENV_DIR   = "venv"
        FLASK_PORT = "5000"
        FLASK_EC2  = "ubuntu@65.0.86.154"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/mohamedfazil2315/Git-Project-.git'
            }
        }

        stage('Install Dependencies (Jenkins Test)') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Basic Test') {
            steps {
                sh '''
                . venv/bin/activate
                python -c "import flask; print('Flask OK')"
                '''
            }
        }

        stage('Deploy to Flask EC2') {
            steps {
                sh '''
                # Create app directory
                ssh ${FLASK_EC2} "mkdir -p ${APP_DIR}"

                # Copy all project files (except venv)
                scp -r * ${FLASK_EC2}:${APP_DIR}

                # Setup environment and restart Flask
                ssh ${FLASK_EC2} "
                  cd ${APP_DIR} &&
                  rm -rf ${VENV_DIR} &&
                  python3 -m venv ${VENV_DIR} &&
                  . ${VENV_DIR}/bin/activate &&
                  pip install --upgrade pip &&
                  pip install -r requirements.txt &&
                  chmod +x dev_flask.sh &&
                  ./dev_flask.sh restart
                "
                '''
            }
        }
    }
}
