import argparse
import os
import random
from time import sleep
import sys
import pdb
import numpy
import requests
from threading import Thread

lamda = 0
finish = 25
args = 0
fp = ""

def generate_requests(args):
    global lamda, finish, ip, check_file
    current_interval = 0

    while current_interval < finish: #if we are not at the end (program of finish seconds)

        num_of_requests = numpy.random.poisson(lamda)

        time_samples = []
        for _ in xrange(num_of_requests): # iterates num_of_requests times
            time_samples.append(numpy.random.uniform(0,1)) #in which parts of the second to send the req per sec messages which ms
            time_samples.sort()


        if len(time_samples) > 0:

            sleep(time_samples[0])
            Thread(target = send_request).start()

            for i in xrange(len(time_samples)-1):
                sleep(time_samples[i+1]-time_samples[i])
                Thread(target = send_request).start()

            sleep(1 - time_samples[-1]) # sleep until the start of the next round

            if args.verbose:
                print "{}: {} requests".format(current_interval, num_of_requests)
                current_interval += 1


def send_request():
    global args
    requests.get(args.ip)

def update_lamda(filename, interval):
    global lamda, finish

    while(finish):
        fp = open(filename, "r")
        sleep(interval)
        temp = fp.readline()
        fp.close()
        if temp[-1]=='\n':
            temp = temp[:-1] # deletes the last character (the new line)
        lamda = int(temp)



if __name__ == "__main__":

    parser = argparse.ArgumentParser(description='argument parser')
    parser.add_argument('--filename',action="store", dest="filename", default="")
    parser.add_argument('--interval', action="store", dest="interval",type=int, default=0)
    parser.add_argument('--init', action="store", dest="init", type=int, default=5)
    parser.add_argument('--ip', action="store", dest="ip", default="http://www.google.com", type=str)
    parser.add_argument('--verbose', action="store", dest="verbose", type=int, default=1)
    args = parser.parse_args()

    lamda = args.init  # take the initial value of lamda

    if "http://" not in args.ip:
        args.ip = "http://" + args.ip  # if user forgot to enter http in ip address :)

    if args.interval > 0: #if the initial value of interval is smaller equal to 0, we deal with only one value throughout the whole run
        try:
            fp = open(args.filename, "r")
            fp.close()

        except:
            sys.exit(1)  # if there is an error in file opening

        check_file = Thread(target=update_lamda, args=(args.filename, args.interval))  # read from file
        check_file.start()


    generate_requests(args)
    finish = 0


    if args.interval > 0:
        check_file.join()

    print "done."
    sys.exit(0)
