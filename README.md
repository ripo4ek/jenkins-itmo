
Запуск:  
1) Запустить через docker-compose up
2) Пароль admin admin
3) Открыть заготовленный пайплайн и запустить

Jenkins будет доступен  по адресу - http://localhost:8080/

Этапы развертки:
1) Создал репозиторий (форк) с приложением 
2) Создал docker-compose фаил и развернул Jenkins через Docker (использован стандартный образ jenkinsci/blueocean)
3) Создал пайплайн
4) Подключил git хук и применил пайплайн скрипт: 
pipeline {
    agent {
        docker {
            image 'python'
            args '-u root:root -p 8000:8000'
        }
    }
    stages {
        stage('pulling') {
            steps {
                git branch: 'main',
                url: 'https://github.com/ripo4ek/identidock'
            }
        }
        stage('testing') {
            steps {
                sh 'python --version'
                sh 'python -m venv env'
                sh 'python -c "import this"'
                sh '. env/bin/activate'
                sh 'pip install --upgrade pip'
                sh 'pip install pytest'
                sh 'pip install -r requirements.txt'
                sh 'cd app'
                sh 'pytest'
            }
        }
    }
}
5) Запустил пайплайн

Заметки:
 - В случае не прохождения тестов/ошибки пулла/инициализации приложения и др. ошибкок связанных с подготовкой приложения к работе, процесс сборки должен быть остановлен с соответсвующей ошибкой.
 - Тесты должны выполняться после завершения полной инициализации приложения и его подготовки к рабочему состоянию.
 - Результат тестов может быть проверен путем просмотра логов работы пайплайна, для удобного отображения рекомендуется использовать плагин Blue Ocean.
