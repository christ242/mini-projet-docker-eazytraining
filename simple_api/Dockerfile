FROM python:2.7-buster
MAINTAINER christ BAGAMBOULA (bagam_fleury)
# Install Packages for dependencies 
RUN DEBIAN_FRONTEND=noninteractive apt-get update -y && apt-get install python-dev python3-dev libsasl2-dev python-dev libldap2-dev libssl-dev -y
# Flask Installation 
RUN pip install flask==1.1.2 flask_httpauth==4.1.0 flask_simpleldap python-dotenv==0.14.0
# Expose the API flask
EXPOSE 5000
# Configure Volume /data
VOLUME ["/data"]
# Copy the script student_age.py to /
COPY student_age.py /
# Run the server python and start api
CMD [ "python", "./student_age.py" ]


