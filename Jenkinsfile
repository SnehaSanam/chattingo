@Library('Jenkins-SharedLibrary') _

pipeline {
 agent any

 environment {
 // Docker Images
 FRONTEND_IMAGE = "chattingo-frontend"
 BACKEND_IMAGE = "chattingo-backend"
 DOCKER_TAG = "latest"
 DOCKERHUB_USER = "snehasanam"
 }
 stages {
 stage('Prepare Env File') {
 steps {
 prepare_env()
 }
 }
 stage('Git Clone') {
 steps {
 script {
 // This calls vars/clone.groovy
 clone('https://github.com/SnehaSanam/chattingo.git', 'main')
 }
 }
 }
 stage('Build Docker Images') {
 steps {
 dockerfrontendbackendimagesbuild(env.FRONTEND_IMAGE, env.BACKEND_IMAGE, env.DOCKER_TAG)
 
 }
 }
 stage("Trivy: Filesystem scan"){
 steps{
 trivy()
 }
 }

 stage('Push to Docker Hub') {
 steps {
 docker_push_frontend_backend(env.FRONTEND_IMAGE, env.BACKEND_IMAGE, env.DOCKER_TAG, env.DOCKERHUB_USER)
 }
 }
 stage("update docker compose"){
 steps{
 update_compose(env.DOCKER_TAG)
 }
 }
 stage('Deploy with Docker Compose') {
 steps {
 docker_compose()
 } 
 }
}
