pipeline {
    agent any
    
    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/romo0208/mydataapp.git', branch: 'master'
            }
        }

        stage('Copy Files to Directory') {
            steps {
                sh '''
                    # Создаем целевую директорию, если её нет
                    mkdir -p /mnt/c/dev/a_data
                    # Копируем содержимое workspace рекурсивно
                    cp -R ./* /mnt/c/dev/a_data/
                    # Или используем rsync для более гибкого копирования
                    # rsync -av --delete ./ /mnt/c/dev/a_data/
                '''
            }
        }        
        
        stage('Start Minikube') {
            steps {
                script {
                    echo "🚀 Запуск Minikube с драйвером Docker..."
                    // --force используется для принудительного перезапуска, если кластер уже существует :cite[1]
                    sh 'nohup minikube start --driver=docker --force > /tmp/minikube.log 2>&1 &'
                    echo "✅ Minikube успешно запущен. Логи в /tmp/minikube.log"
                    sleep time: 15, unit: 'SECONDS'
                    sh 'cat /tmp/minikube.log'
                }
            }
        }
        
        stage('Build Frontend Image') {
            steps {
                script {
                    echo "🔨 Сборка образа фронтенда..."
                    dir('/mnt/c/dev/a_data/frontend-app') {
                        // Сборка образа внутри Minikube environment :cite[1]
                        sh 'minikube image build -t frontend-app_frontend:latest .'
                    }
                    echo "✅ Образ фронтенда успешно собран."
                }
            }
        }
        
        stage('Build Backend Image') {
            steps {
                script {
                    echo "🔨 Сборка образа бэкенда..."
                    dir('/mnt/c/dev/a_data/postgres-java-app') {
                        // --overwrite=true позволяет перезаписать существующий образ :cite[1]
                        sh 'minikube image build -t postgres-java-app_app:latest .'
                    }
                    echo "✅ Образ бэкенда успешно собран."
                }
            }
        }
        
        stage('Load Frontend Image to Minikube') {
            steps {
                script {
                    echo "📦 Загрузка образа фронтенда в Minikube..."
                    sh 'minikube image load frontend-app_frontend:latest --overwrite=true'
                    echo "✅ Образ фронтенда загружен в Minikube."
                }
            }
        }
        
        stage('Load Backend Image to Minikube') {
            steps {
                script {
                    echo "📦 Загрузка образа бэкенда в Minikube..."
                    // --overwrite=true для принудительной замены существующего образа :cite[1]
                    sh 'minikube image load postgres-java-app_app:latest --overwrite=true'
                    echo "✅ Образ бэкенда загружен в Minikube."
                }
            }
        }
        
        stage('Apply Kubernetes Manifests') {
            steps {
                script {
                    echo "🚀 Применение Kubernetes манифестов..."
                    dir('/mnt/c/dev/a_data/k8s') {
                        // Сначала применяем namespace, так как от него зависят другие ресурсы
                        sh 'kubectl apply -f mydataapp-namespace.yaml'
                        
                        // Применяем все остальные YAML-файлы в каталоге, кроме namespace
                        def files = findFiles(glob: '*.yaml')
                        files.each { file ->
                            if (file.name != 'mydataapp-namespace.yaml') {
                                sh "kubectl apply -f ${file.name}"
                            }
                        }
                    }
                    echo "✅ Все манифесты успешно применены."
                }
            }
        }
        
//        stage('Start Minikube Tunnel') {
//            steps {
//                script {
//                    echo "🔗 Запуск Minikube tunnel для доступа к сервисам..."
//                    // Запуск tunnel в фоновом режиме :cite[5]
//                    sh 'nohup minikube tunnel > /tmp/minikube-tunnel.log 2>&1 &'
//                    echo "✅ Minikube tunnel запущен. Логи в /tmp/minikube-tunnel.log"
//                    
//                    // Небольшая пауза для стабилизации tunnel
//                    sleep time: 10, unit: 'SECONDS'
//                    sh 'cat /tmp/minikube-tunnel.log'
//                    sleep time: 900, unit: 'SECONDS'
//                }
//            }
//        }

        stage('Start Minikube dashboard') {
            steps {
                script {
                    echo "🔗 Запуск Minikube dashboard для доступа к сервисам..."
                    // Запуск tunnel в фоновом режиме
                    sh 'nohup minikube dashboard > /tmp/minikube-dashboard.log 2>&1 &'
                    echo "✅ Minikube dashboard запущен. Логи в /tmp/minikube-dashboard.log:"                    
                    // Небольшая пауза для стабилизации dashboard
                    sleep time: 15, unit: 'SECONDS'
                    sh 'cat /tmp/minikube-dashboard.log'
                }
            }
        }    
       
    }
    
    post {
        always {
            echo "📝 Pipeline завершен. Детали можно проверить в логах выше."
        }
        success {
            echo "🎉 Все стадии выполнены успешно!"
            // Можно добавить уведомление, например, в Slack или email
        }
        failure {
            echo "❌ Pipeline завершился с ошибкой. Проверьте логи для диагностики."
            // Можно добавить уведомление об ошибке
        }
    }
}