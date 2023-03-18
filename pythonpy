from calendar import c
from tkinter import TRUE
from selenium import webdriver
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.firefox.options import Options
from selenium.webdriver.common.keys import Keys
from selenium.common.exceptions import NoSuchElementException
from selenium.webdriver.common.by import By
from datetime import datetime
from os.path import exists
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.support import expected_conditions as EC

# pour colorer les prints
import colorama
# pour colorer les prints
from colorama import Fore
from colorama import Style

#Stack tech
from subprocess import check_output
import requests
import urllib.request
from urllib.parse import ParseResult, urlparse
import pandas as pd
from datetime import datetime
from openpyxl import load_workbook

import os
import os.path
import re
import time
import json
import random
from datetime import datetime

date = datetime.today().strftime('%Y-%m-%d')

colorama.init()
# stockage du path du script
# For Python 3
check_output('curl -L "https://docs.google.com/spreadsheets/d/1a9wabH5oSOuzv6fQcR2wkETQu3M5OybyLDH7cPXxT_4/export?gid=0&format=xlsx" > ./populations.xlsx', shell=True)
currentDir = os.path.abspath(os.getcwd())
currentDir = currentDir.replace('\\', '/')
list_search = list()
# Dossier à creer
directory = "stockageOffreIndeed" + date
firefox_options = Options()
firefox_options.add_argument('— disk-cache-size=0')
firefox_options.add_argument('--disable-blink-features=AutomationControlled')
caps = webdriver.DesiredCapabilities().FIREFOX
caps["marionette"] = True

def RefreshExcel():
    # connection à l'excel préalablement téléchargé
    df = pd.read_excel(r'populations.xlsx')
    # Entrer les lignes à scrap
    line = input("Entrer UNE  LIGNE A SCRAP : ( 0 SI OKAY ) \n")
    line = int(line)
    line = line-2
    if line < 0:
        return None
    else:
        title_job = " " + (df.at[line, 'Profession 1'])
        title_ville = " " + (df.at[line, 'Département / GV'])
        list_search.append({'title_job': title_job, 'title_ville': title_ville})
        print(list_search)
        RefreshExcel()
    


def scroll_function(i, driver):
    height = i * 1000
    time.sleep(1.3)
    driver.execute_script(
        "window.scrollTo(" + str(height) + ", " + str(height) + ")")
    time.sleep(1.3)


def timeSleeper():
    randomTime = random.randint(1, 3)
    print(Fore.BLUE + str(randomTime) + "sec en attente via la fonction timeSleeper; retirer la pour aller plus vite !!attention si vous faites ca vous avez des risques de ban ip, activez votre VPN")
    print(Style.RESET_ALL)
    time.sleep(randomTime)


TAG_RE = re.compile(r'<[^>]+>')


def remove_tags(description):
    return TAG_RE.sub(' ', description)


def recapcha(driver):
    try:
        driver.find_element(By.XPATH, "//a[contains(@onclick,'closeGoogleOnlyModal')]").click()
        recapcha = driver.find_element(By.ID, "popover-background").click()
        recapcha = driver.find_element(By.ID, "popover-background").click()
    except NoSuchElementException:  # spelling error making this code not work as expected
        pass

def recapchalinkedin(driver):
    try:
        driver.find_element(By.XPATH, "//a[contains(@data-control-name,'ga-cookie.consent.accept.v4')]").click()
    except NoSuchElementException:  # spelling error making this code not work as expected
        pass

def initGoogle(driver):
    cookieGoogle = driver.find_element(By.ID, 'L2AGLb').click()
    try:
        driver.find_element(By.CLASS_NAME, 'h-captcha')
        input(Fore.BLUE + 'Captcha à résoudre veuillez le résoudre et tapez entrez pour continuer...')
        print(Style.RESET_ALL)
    except NoSuchElementException:
        print("No captcha")

    if cookieGoogle:
        print("GOOGLE a changé l'id recupere le nouveau")
    else:
        print("Init Google...")


def scrapIndeed():
    driver_indeed = webdriver.Firefox(executable_path=r'C:\Python310\geckodriver.exe')
    driver_indeed.get("https://google.com/")
    initGoogle(driver_indeed)
    list_indeed = json.dumps(list_search)
    #json_raw= list_indeed.readlines()
    list_indeed = json.loads(list_indeed)
    for item in list_indeed:
        urlParent = 'https://fr.indeed.com/jobs?q='+item["title_job"]+'&l='+ item["title_ville"]
        urlParent = urlParent + "&fromage=3&limit=25&sort=date&filter=0"
        # parsing de l'url pour récupérer le titre du job et la ville du job a scrapé
        urlParse = urlParent.split("?")
        urlParse = urlParse[1].split("&")
        urlTitreJob = urlParse[0].split("=")
        urlVilleJob = urlParse[1].split("=")
        urlTitreJob = urlTitreJob[1]
        urlVilleJob = urlVilleJob[1]
        urlFinal = urlTitreJob + "--" + urlVilleJob
        print(urlFinal)
        
        driver_indeed.get(urlParent)
        timeSleeper()
            
        listeDeLiensNonClean = list()
        listeDeLiensClean = list()
        offresIndeed = list()
        compteurBouclesListeDates = 0 
        #On attend la page charge
        print("On attend le site charge ...")

        try:
            onetrust = driver_indeed.find_element(By.XPATH,'//*[@id="onetrust-reject-all-handler"]').click()
        except NoSuchElementException:
            pass
        timeSleeper()
        print("Cookie validé")

        
        forJson = str(compteurBouclesListeDates) + "--" + urlFinal
        compteurBouclesListeDates += 1

        links = driver_indeed.find_elements(By.CLASS_NAME,'jcs-JobTitle')
        nombreDeLiens = len(links)
        if (nombreDeLiens < 4):
            print(urlParent)
            recapcha(driver_indeed)
            timeSleeper()
            links = driver_indeed.find_elements(By.XPATH,'/html/body/table/tbody/tr/td/table/tbody/tr/td/div/div/a')
            nombreDeLiens = len(links)
        # commenté cette ligne elif si vous voulez scrapper plus de 10jobs
        if (nombreDeLiens > 17):
            nombreDeLiens = 17
            print(nombreDeLiens)
        else:
            print(nombreDeLiens)
        
        comptePour12Liens = 0
        while comptePour12Liens < nombreDeLiens:
            link = (links[comptePour12Liens].get_attribute('href'))
            listeDeLiensNonClean.append(link)
            comptePour12Liens = comptePour12Liens + 1

        print(listeDeLiensNonClean)
        print(len(listeDeLiensNonClean))
        
        # on nettoye les 2 premiers liens de la base de donnée car il ne sont pas utiles
        cleanLinks = []
        compteurLink = 2

        while compteurLink < nombreDeLiens:
            listeDeLiensClean.append(listeDeLiensNonClean[compteurLink])
            compteurLink = compteurLink +1
        
        
        if (listeDeLiensClean == 0):
            print(Fore.RED + "problem occured with xpath...")
            print(Style.RESET_ALL)
            try:
                onetrust = driver_indeed.find_element(By.XPATH,'//*[@id="onetrust-reject-all-handler"]').click()
            except NoSuchElementException:
                pass
            timeSleeper()
            print("Cookie validé")

            links = driver_indeed.find_elements(By.CLASS_NAME,'jcs-JobTitle')
            nombreDeLiens = len(links)
            if (nombreDeLiens < 4):
                print(urlParent)
                recapcha(driver_indeed)
                timeSleeper()
                links = driver_indeed.find_elements(By.XPATH,'/html/body/table[2]/tbody/tr/td/table/tbody/tr/td[1]/div[4]/div/ul/li/div/div[1]/div/div[1]/div/table[1]/tbody/tr/td/div[1]/h2')
                nombreDeLiens = len(links)
            # commenté cette ligne elif si vous voulez scrapper plus de 10jobs
            elif (nombreDeLiens > 17):
                nombreDeLiens = 17
                print(nombreDeLiens)
            else:
                print(nombreDeLiens)
            
            comptePour12Liens = 0
            while comptePour12Liens < nombreDeLiens:
                link = (links[comptePour12Liens].get_attribute('href'))
                listeDeLiensNonClean.append(link)
                comptePour12Liens = comptePour12Liens + 1

            print(listeDeLiensNonClean)
            print(len(listeDeLiensNonClean))
            
            # on nettoye les 2 premiers liens de la base de donnée car il ne sont pas utiles
            cleanLinks = []
            compteurLink = 2

            while compteurLink < nombreDeLiens:
                listeDeLiensClean.append(listeDeLiensNonClean[compteurLink])
                compteurLink = compteurLink +1

        print(len(listeDeLiensClean))
        # print(listeDeLiensClean)

        # input d'attente facultatif
        # input("//////////// RESULTAT A L'ECRAN \n")

        # mettre nombre d'offre desire à 25 pour récuper le max de lien
        # offresLiens = 0
        # nombreDoffresDesire = 10

        for scrappingUrl in listeDeLiensClean:
            # while offresLiens <= nombreDoffresDesire:
            driver_indeed.get(scrappingUrl)
            print('waiting 2sec...')
            timeSleeper()
            recapcha(driver_indeed)

            # stockage de la date actuelle
            date = datetime.today().strftime('%Y-%m-%d-%H:%M:%S')
            print(date)

            # récupération de l'url
            url = scrappingUrl
            print(url)

            # récupération du titre
            titre = driver_indeed.find_elements(By.XPATH,'//div[contains(@class, "jobsearch-JobInfoHeader-title-container")]//h1[contains(@class, "jobsearch-JobInfoHeader-title")]')
            for titreLien in titre: 
                titre = (titreLien.get_attribute('innerHTML'))
                if titre == '[]':
                    print(Fore.RED + 'titre bugged, stopping the loop')
                    titreLien[titre] = None
                    myDict = {}
                    myDict["date"] = date
                    myDict["url"] = url
                    myDict["titre"] = titre
                    myDict["ville"] = ville
                    myDict["contrat"] = contrat
                    myDict["description"] = description
                    myDict["salary"] = salary
                    myDict["metier"] = metier
                    myDict["statut"] = statut
                    myDict["secteur"] = secteur
                    myDict["experience"] = experience
                    offresIndeed.append(myDict)

                    with open(f"{forJson}.json", "wb") as writeJSON:
                        jsStr = json.dumps(offresIndeed)
                        # the decode() needed because we need to convert it to binary
                        writeJSON.write(jsStr.encode('utf-8')) 
                        print ('end')

                    print(Style.RESET_ALL)
                    break
            print(titre)
            metier = titre

            # récupération de la ville
            try:
                ville = driver_indeed.find_element(By.XPATH,'/html/body/div[1]/div[1]/div/div[3]/div/div/div[1]/div[1]/div/div[1]/div[2]/div/div/div/div[2]')
                ville = ville.text
            except NoSuchElementException:  #spelling error making this code not work as expected
                ville = "France"
            print(ville)

            # récupération de la description
            try:
                description = driver_indeed.find_element(By.XPATH,'//div[contains(@id, "jobDescriptionText")]')
                description = description.text
            except NoSuchElementException:  #spelling error making this code not work as expected
                try:
                    description = driver_indeed.find_element(By.ID,'jobDescriptionText')
                    description = description.text
                except NoSuchElementException:
                    description = "Contactez l'employeur"
                    pass
            # else:
            #     

            print(description)
            timeSleeper()

            # récupération du salaire
            try:
                salary = driver_indeed.find_element(By.XPATH, '/html/body/div[1]/div[1]/div/div[3]/div/div/div[1]/div[1]/div[2]/div[2]/div[1]/div/span[1]')
                salary = salary.text
            except NoSuchElementException:
                salary = 'A définir'
                print(Fore.RED + 'bug xpath salary')
                print(Style.RESET_ALL)
                pass
            print(salary)

            # récupération du type de contrat
            try:
                contrat = driver_indeed.find_element(By.XPATH, '/html/body/div[1]/div[1]/div/div[3]/div/div/div[1]/div[1]/div[2]/div[2]/div[1]/div/span[2]')
                contrat = contrat.text
            except NoSuchElementException:  #spelling error making this code not work as expected
                contrat = "CDI"
                pass
            print(contrat)
            statut = contrat

            # récupération du secteur
            try:
                secteur = titre.text.split()[0]
            except AttributeError:
                secteur = titre
            else:
                secteur = titre.text.split()[0]

            experience = "Tous niveaux d'expérience acceptés"

            myDict = {}
            myDict["date"] = date
            myDict["url"] = url
            myDict["titre"] = titre
            myDict["ville"] = ville
            myDict["contrat"] = contrat
            myDict["description"] = description
            myDict["salary"] = salary
            myDict["metier"] = metier
            myDict["statut"] = statut
            myDict["secteur"] = secteur
            myDict["experience"] = experience
            offresIndeed.append(myDict)
            # offresLiens = offresLiens + 1

        #   ##   ##   ##   ##   ##   ##   ##   ##   ##   ##   ##   ##   ##   ##   ##   #
        #scrapLinkedin(item["title_job"], item["title_ville"], offresIndeed)
        with open(f"job_indeed_linkedin/{forJson}.json", "wb") as writeJSON:
            jsStr = json.dumps(offresIndeed)
            # the decode() needed because we need to convert it to binary
            writeJSON.write(jsStr.encode('utf-8')) 
            print ('end')
        print(item["title_job"]+ "----------------------------------------------------------------------------------------------------------------FAIT")
        poster(offresIndeed, driver_indeed)
    driver_indeed.close()

def sortLinkedin(driver):
    currentUrl = driver.current_url
    #driver.close()
    #driver.quit()
    currentUrl = currentUrl + "&f_TPR=r604800"
    #"---------------"
    driver.delete_all_cookies()
    driver.execute_script("window.sessionStorage.clear();")
    driver.execute_script("window.localStorage.clear();")
    
    #driver = webdriver.Firefox(executable_path=r'C:\Python310\geckodriver.exe')
    driver.get("https://google.com/")
    
    login(driver)
    driver.get(currentUrl)
    recapchalinkedin(driver)
    #On valide les cookies

def getLink_Linkedin(driver, my_list_linkedin):
       
    links_linkedin = driver.find_elements_by_xpath('//a[contains(@class, "job-card-container__link job-card-list__title")]')
    count = (len(links_linkedin))
    if count < 4:
        time.sleep(2)
        links_linkedin = driver.find_elements_by_xpath('//a[contains(@class, "job-card-container__link job-card-list__title")]')
        count = (len(links_linkedin))
        print(count)
    else:
        print(count)
    
    for i in links_linkedin:
        step1 = (i.get_attribute('href'))
        my_list_linkedin.append(step1)


def login(driver):
    driver.get("https://www.linkedin.com/login?fromSignIn=true&trk=guest_homepage-basic_nav-header-signin")
    usernameElement = driver.find_elements_by_xpath('//*[@id="username"]')
    time.sleep(1)
    for iusername in usernameElement:
        nomusername = iusername.send_keys("sauvegarde.pmgroupefrance@gmail.com")
        print("username ADD")
    time.sleep(1)
    
    passwordElement = driver.find_elements_by_xpath('//*[@id="password"]')
    time.sleep(1)
    time.sleep(1)
    for jpassword in passwordElement:
        passwordElement.clear()
        n_password = jpassword.send_keys("63t7aK6nTo5L")
        print("password ADD")
    time.sleep(2)
    divs = driver.find_element_by_xpath('//div[contains(@class, "login__form_action_container ")]')
    parent_elem  = divs.find_element_by_xpath('//button[contains(@type, "submit")]')
    parent_elem.click()
    time.sleep(2)
    time.sleep(2)
    login = TRUE


def scrapLinkedin(title_job, title_ville, offresIndeed):
    my_list_linkedin = list()

    driver_linkedin = webdriver.Firefox(options=firefox_options,  capabilities=caps,executable_path=r'C:\Python310\geckodriver.exe')
    driver_linkedin.get("https://google.com/")
    initGoogle(driver_linkedin)
    driver_linkedin.get("https://fr.linkedin.com/jobs/search")
    recapchalinkedin(driver_linkedin)
    #On valide les cookies

    inputElement = driver_linkedin.find_elements_by_xpath('//input[contains(@aria-controls, "job-search-bar-keywords-typeahead-list")]')
    time.sleep(1)
    for i in inputElement:
        nom = i.send_keys(title_job)
        print("metier ADD")
    time.sleep(1)
    
    locationElement = driver_linkedin.find_elements_by_xpath('//input[contains(@aria-controls, "job-search-bar-location-typeahead-list")]')
    time.sleep(1)
    for j in locationElement:
        locationElement.clear()
        n_ = j.send_keys(title_ville + " ")
        print("location ADD")
    time.sleep(2)
    driver_linkedin.find_element_by_xpath('/html/body/div[3]/header/nav/section/section[2]/form/button').click()
    
    time.sleep(1)
    #On attend la page charge
    print("On attend le site charge ...")
    #login
    sortLinkedin(driver_linkedin)
    time.sleep(1)
    time.sleep(2)

    """for i in range(2):
        time.sleep(2)
        source = driver_linkedin.find_elements_by_xpath('//a[contains(@class, "job-card-container__link job-card-list__title")]')[0]
        target = driver_linkedin.find_elements_by_xpath('//a[contains(@class, "job-card-container__link job-card-list__title")]')[6]
        action = ActionChains(driver_linkedin)
        action.drag_and_drop(source, target).perform()

        scroll = driver_linkedin.find_elements_by_xpath('//ul[contains(@class, "jobs-search-results__list")]')

        actions = ActionChains(driver_linkedin)

        actions.move_to_element(scroll).perform()
        scroll=driver_linkedin.find_elements_by_xpath('/html/body/div[7]/div[3]/div[3]/div[2]/div/section[1]/div/div')
        scroll.send_keys(Keys.PAGE_DOWN)
        time.sleep(1.3)"""
        #driver_linkedin.execute_script("window.scrollTo(0, document.body.scrollHeight);")
        #scroll_function(i)"""
    
    getLink_Linkedin(driver_linkedin, my_list_linkedin)
    
        
    print("on a tout les liens des offres")
    print(my_list_linkedin)
    
    link_driver = webdriver.Firefox(capabilities=caps,executable_path=r'C:\Python310\geckodriver.exe')
    link_driver.get("https://google.com/")
    #for increment in range(1, count):
    for j in my_list_linkedin:
        link_driver.get(j)
        time.sleep(2)
        recapchalinkedin(link_driver)
        print('ok on a ttend 2sec---------------------------------------------------------------------------------------')
        
        #countReslut = driver.find_element_by_xpath("/html/body/table[2]/tbody/tr/td/table/tbody/tr/td[1]/div[5]/div/a["+ str(increment) +"]").click()

        date = datetime.today().strftime('%Y-%m-%d-%H:%M:%S')
        print(date)

        url = j
        print(url)
        titre = link_driver.find_elements_by_xpath('//div[contains(@class, "top-card-layout__entity-info")]//h1[contains(@class, "topcard__title")]')
        for i in titre: 
            titre = (i.get_attribute('innerHTML'))
        print(titre)

        try:
            villes = link_driver.find_elements_by_xpath('//div[contains(@class, "topcard__flavor-row")]//span[contains(@class, "topcard__flavor--bullet")]')
            for i_ville in villes:
                ville = i_ville.text
                ville = ville + ", France"
        except NoSuchElementException:  #spelling error making this code not work as expected
            ville = "France"
        print(ville)


        time.sleep(1)

        try:
            descriptions = link_driver.find_elements_by_xpath('//section[contains(@class, "show-more-less-html")]//div[contains(@class, "show-more-less-html__markup")]')
            for i_description in descriptions:
                description = (i_description.get_attribute('innerHTML'))
                description = remove_tags(description)
        except NoSuchElementException:  #spelling error making this code not work as expected
            description = "Contactez l'employeur"
            pass
        print(description)

        
        salary = "A Définir"
        print(salary)

        try:
            contrat = "CDI"
            contrats = link_driver.find_elements_by_xpath('/html/body/main/section[1]/div/div[1]/section[1]/div/ul/li[2]/span')
            for i_contrat in contrats:
                contrat = (i_contrat.get_attribute('innerHTML'))
                contrat = remove_tags(contrat)
        except NoSuchElementException:  #spelling error making this code not work as expected
            contrat = "CDI"
            pass
        print(contrat)
        
        
        try:
            metier = title_job
            metiers = link_driver.find_elements_by_xpath('/html/body/main/section[1]/div/div[1]/section[1]/div/ul/li[3]/span')
            for i_metier in metiers:
                metier = (i_metier.get_attribute('innerHTML'))
        except NoSuchElementException:  #spelling error making this code not work as expected
            metier = title_job
            pass
        print(metier)

        try:
            statut = " A DEFINIR"
            statuts = link_driver.find_elements_by_xpath('/html/body/main/section[1]/div/div[1]/section[1]/div/ul/li[1]/span')
            for i_statut in statuts:
                statut = (i_statut.get_attribute('innerHTML'))
        except NoSuchElementException:  #spelling error making this code not work as expected
            statut = " A DEFINIR"
            pass
        print(statut)

        try:
            secteur = title_job
            secteurs = link_driver.find_elements_by_xpath('/html/body/main/section[1]/div/div[1]/section[1]/div/ul/li[4]/span')
            for i_secteur in secteurs:
                secteur = (i_secteur.get_attribute('innerHTML'))
        except NoSuchElementException:  #spelling error making this code not work as expected
            secteur = title_job
            pass
        print(secteur)

        experience = statut


        myDict = {}
        myDict["date"] = date
        myDict["url"] = url
        myDict["titre"] = titre
        myDict["ville"] = ville
        myDict["contrat"] = contrat
        myDict["description"] = description
        myDict["salary"] = salary
        myDict["metier"] = metier
        myDict["statut"] = statut
        myDict["secteur"] = secteur
        myDict["experience"] = experience

        offresIndeed.append(myDict)
    link_driver.close()
    driver_linkedin.close()



def scrapMonster():
    driver_monster = webdriver.Firefox(executable_path=r'C:\Python310\geckodriver.exe')
    driver_monster.get("https://google.com/")
    initGoogle(driver_monster)


def loginTuc(driver):
    time.sleep(2)
    driver.get("https://trouver-un-candidat.com/wp-admin/")
    user_login_Element = driver.find_elements_by_xpath('//*[@id="user_login"]')
    time.sleep(1)
    for iuser_login_ in user_login_Element:
        nomuser_login_ = iuser_login_.send_keys("annoncetrouveruncandidat")
        print("user_login_ ADD")
    time.sleep(1)
    
    user_passElement = driver.find_elements_by_xpath('//*[@id="user_pass"]')
    time.sleep(1)
    time.sleep(1)
    for juser_pass in user_passElement:
        user_passElement.clear()
        n_user_pass = juser_pass.send_keys("(%)Sku&mv#35OewiC%")
        print("user_pass ADD")
    time.sleep(2)
    divs = driver.find_element_by_xpath('//*[@id="wp-submit"]')
    divs.click()
    time.sleep(2)
    login = TRUE

def poster(offresIndeed, driver):
    print('postage .........')
    time.sleep(2)
    loginTuc(driver)
    driver.get("https://trouver-un-candidat.com/wp-admin/post-new.php?post_type=job_listing")

    offresIndeed = json.dumps(offresIndeed)
    #json_raw= list_indeed.readlines()
    offresIndeed = json.loads(offresIndeed)
    for item_job in offresIndeed:
        time.sleep(2)
        try:
            time.sleep(2)
            
            divs = driver.find_element_by_xpath('//div[contains(@class, "components-modal__header-heading-container")]')
            parent_elem  = divs.find_element_by_xpath('//button[contains(@aria-label, "Fermez la boite de dialogue")]').click()
            time.sleep(1)
            print("yes popup")
        except NoSuchElementException:
            print("NO popup")
            pass

        try:
            driver.find_element_by_class_name('interface-interface-skeleton__body').click()
            time.sleep(1)
        except NoSuchElementException:
            pass

        try:
            driver.find_element_by_class_name('editor-post-title__input').send_keys(item_job['titre'])
            time.sleep(1)
        except NoSuchElementException:
            pass

        try:
            phonenum = driver.find_element_by_xpath('//div[contains(@data-title, "Paragraphe")]')
            #phonenum.send_keys(item_job['description'])
            ActionChains(phonenum).send_keys(item_job['description']).perform()
            #driver.find_element_by_class_name('block-editor-block-list__layout').click()
            #driver.find_element_by_class_name('block-editor-block-list__layout').send_keys(Keys.ENTER)
            #driver.find_element_by_class_name('block-editor-block-list__layout').send_keys(item_job['description'])
            time.sleep(1)
        except NoSuchElementException:
            pass
RefreshExcel()

scrapIndeed()

#
# poster()
#scrapLinkedin("Responsable de magasin", "Paris", offresIndeed = list())
"""
scrapIndeed()
scrapLinkedin()
scrapMonster()
"""
